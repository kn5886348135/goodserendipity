---
title: How to do distributed locking
author: Louis
date: 2023-08-16 13:07:09
categories: [Distributed System]
tags: [DLM, distributed locking]
---

&emsp;&emsp;Martin Kleppmann的[How to do distributed locking](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html)是对Redis的Redlock算法的评论。原文翻译如下。

&emsp;&emsp;作为我的书研究的一部分，我在 Redis 网站上发现了一种名为 Redlock 的算法。该算法声称在 Redis 之上实现容错分布式锁（或者更确切地说，租赁([Leases An Efficient Fault-Tolerant Mechanism for Distributed File Cache Consistency](https://www.goodserendipity.com/asserts/distributed-system/Leases%20An%20Efficient%20Fault-Tolerant%20Mechanism%20for%20Distributed%20File%20Cache%20Consistency.pdf))），并且该页面请求分布式系统人员的反馈。该算法本能地在我的脑海中敲响了警钟，所以我花了一些时间思考它并写下了这些笔记。

&emsp;&emsp;由于 Redlock 已经有超过 10 个独立实现，而且我们不知道谁已经在依赖这个算法，所以我认为值得公开分享我的笔记。 我不会讨论 Redis 的其他方面，其中一些方面已经在其他地方受到了批评。

&emsp;&emsp;在详细介绍 Redlock 之前，我要说的是，我非常喜欢 Redis，并且我过去已经在生产中成功地使用过它。 我认为它非常适合您想要在服务器之间共享一些瞬态、近似、快速变化的数据的情况，并且如果您偶尔由于某种原因丢失这些数据也没什么大不了的。 例如，一个好的用例是维护每个 IP 地址的请求计数器（用于速率限制目的）和每个用户 ID 的不同 IP 地址集（用于滥用检测）。

&emsp;&emsp;然而，Redis 已经逐渐进军数据管理领域，这些领域对一致性和持久性有更强的期望——这让我很担心，因为这不是 Redis 的设计目的。 可以说，分布式锁定就是其中之一。 让我们更详细地研究一下。

### 你用那个锁做什么？

&emsp;&emsp;锁的目的是确保在可能尝试执行同一工作的多个节点中，只有一个节点实际执行该操作（至少一次只有一个）。 这项工作可能是将一些数据写入共享存储系统、执行一些计算、调用一些外部 API 等。 从高层次来看，您可能需要在分布式应用程序中使用锁有两个原因：为了效率或为了正确性([The Chubby lock service for loosely-coupled distributed systems](https://www.goodserendipity.com/asserts/distributed-system/The%20Chubby%20lock%20service%20for%20loosely-coupled%20distributed%20systems.pdf))。 为了区分这些情况，你可以询问如果锁失败会发生什么：

> **效率** 采取锁定可以避免不必要地重复相同的工作（例如一些昂贵的计算）。如果锁定失败并且两个节点最终执行相同的工作，结果是成本略有增加（您最终向 AWS 支付的费用比其他情况下多了 5 美分）或造成了轻微的不便（例如，用户最终 收到相同的电子邮件通知两次）。

> **正确性** 锁定可以防止并发进程互相干扰并扰乱系统状态。如果锁定失败并且两个节点同时处理同一数据，则会导致文件损坏、数据丢失、永久不一致、给患者服用的药物剂量错误或其他一些严重问题。

&emsp;&emsp;两者都是想要锁的有效情况，但您需要非常清楚您正在处理两者中的哪一个。

&emsp;&emsp;我认为，如果您只是为了提高效率而使用锁，则没有必要承担 Redlock 的成本和复杂性，运行 5 个 Redis 服务器并检查多数来获取锁。您最好只使用单个 Redis 实例，也许可以异步复制到辅助实例，以防主实例崩溃。

&emsp;&emsp;如果您使用单个 Redis 实例，那么如果您的 Redis 节点突然断电或出现其他问题，您当然会丢失一些锁。 但如果您只是将锁用作效率优化，并且崩溃不会经常发生，那就没什么大不了的。 这种“没什么大不了”的场景正是 Redis 的闪光点。至少如果您依赖于单个 Redis 实例，每个查看系统的人都清楚这些锁是近似的，并且仅用于非关键目的。

&emsp;&emsp;另一方面，Redlock 算法具有 5 个副本和主节点投票，乍一看似乎它适合锁定对于正确性很重要的情况。我将在以下几节中论证它不适合该目的。 在本文的其余部分中，我们将假设您的锁对于正确性很重要，并且如果两个不同的节点同时认为它们持有相同的锁，则这是一个严重的错误。

### 用锁保护资源

&emsp;&emsp;让我们暂时将 Redlock 的细节放在一边，讨论一下分布式锁的一般使用方式（与使用的特定锁定算法无关）。重要的是要记住，分布式系统中的锁与多线程应用程序中的互斥体不同。 这是一个更复杂的野兽，因为不同的节点和网络都可能以各种方式独立地失败。

&emsp;&emsp;例如，假设您有一个应用程序，其中一个客户端需要更新共享存储（例如 HDFS 或 S3）中的文件。一个客户端首先获取锁，然后读取文件，进行一些更改，将修改后的文件写回，最后释放锁。该锁可防止两个客户端同时执行将导致更新丢失的读取-修改-写入循环。代码可能看起来像这样：

```JavaScript
function writeData(filename, data) {
    var lock = lockService.acquireLock(filename);
    if (!lock) {
        throw 'Failed to acquire lock';
    }

    try {
        var file = storage.readFile(filename);
        var updated = updateContents(file, data);
        storage.writeFile(filename, updated);
    } finally {
        lock.release();
    }
}
```

&emsp;&emsp;不幸的是，即使你有完美的锁服务，上面的代码也被破坏了。 下图显示了如何导致数据损坏：
![不安全的锁](https://www.goodserendipity.com/asserts/distributed-system/unsafe-lock1.png)

&emsp;&emsp;在此示例中，获取锁的客户端在持有锁的同时会暂停很长一段时间，例如因为垃圾收集器 (GC) 启动。锁有超时（即，它是租约），这总是一个好主意（否则崩溃的客户端可能会永远持有锁并且永远不会释放它）。 然而，如果 GC 暂停的持续时间超过了租约到期时间，并且客户端没有意识到它已经到期，它可能会继续进行一些不安全的更改。

&emsp;&emsp;这个错误不是理论上的：HBase 曾经有过这个问题([ZooKeeper: Distributed Process Coordination](https://www.goodserendipity.com/asserts/zookeeper/O'Reilly.ZooKeeper.Distributed%20process%20coordination.2013.pdf) 和 [HBase and HDFS: Understanding filesystem usage in HBase](https://www.slideshare.net/enissoz/hbase-and-hdfs-understanding-filesystem-usage))。 通常，GC 暂停时间非常短，但众所周知，“stop-the-world”GC 暂停有时会持续几分钟 [Avoiding Full GCs in Apache HBase with MemStore-Local Allocation Buffers: Part 1](https://blog.cloudera.com/) – 当然足够长，足以让租约到期。 即使像 HotSpot JVM 的 CMS 这样所谓的“并发”垃圾收集器也无法与应用程序代码完全并行运行——即使它们需要时不时地停止运行 [Java Garbage Collection Distilled](https://mechanical-sympathy.blogspot.com/2013/07/java-garbage-collection-distilled.html)。

&emsp;&emsp;您无法通过在写回存储之前插入锁定到期检查来解决此问题。请记住，GC 可以在任何点暂停正在运行的线程，包括对您来说非常不方便的点（在最后一次检查和写入操作之间）。

&emsp;&emsp;如果您因编程语言运行时没有长时间的 GC 暂停而感到沾沾自喜，那么还有许多其他原因可能导致您的进程暂停。 也许您的进程尝试读取尚未加载到内存中的地址，因此它会出现页面错误并暂停，直到从磁盘加载页面为止。 也许您的磁盘实际上是 EBS(Elastic Block Store)，因此读取变量不知不觉地变成了 Amazon 拥塞网络上的同步网络请求。 也许有许多其他进程争夺 CPU，并且您在调度程序树中遇到了黑色节点。 也许有人不小心向进程发送了 SIGSTOP。不管怎样，您的进程将暂停。

&emsp;&emsp;如果您仍然不相信我关于进程暂停的说法，请考虑文件写入请求在到达存储服务之前可能会在网络中延迟。以太网和 IP 等数据包网络可能会任意延迟数据包，而且确实如此[The Network is Reliable](https://www.goodserendipity.com/asserts/distributed-system/The%20Network%20is%20Reliable.pdf)：在 GitHub 上的一个著名事件[Downtime last Saturday](https://github.blog/2012-12-26-downtime-last-saturday/)中，数据包在网络中延迟了大约 90 秒。这意味着应用程序进程可能会发送写请求，并且可能会在租约已经过期的情况下在一分钟后到达存储服务器。

&emsp;&emsp;即使在管理良好的网络中，这种事情也可能发生。您根本无法对时间做出任何假设，这就是为什么上面的代码从根本上来说是不安全的，无论您使用什么锁服务。

### 使用围栏确保锁的安全

&emsp;&emsp;这个问题的修复实际上非常简单：您需要在对存储服务的每个写入请求中包含一个栅栏令牌。在这种情况下，防护令牌只是一个每次客户端获取锁时都会增加的数字（例如，由锁服务递增）。如下图所示：
![栅栏令牌锁](https://www.goodserendipity.com/asserts/distributed-system/fencing-tokens.png)

&emsp;&emsp;客户端 1 获取租约并获得令牌 33，但随后它进入长时间暂停状态并且租约到期。客户端 2 获取租约，获取令牌 34（数字始终增加），然后将其写入发送到存储服务，包括 34 的令牌。稍后，客户端 1 恢复正常并将其写入发送到存储服务 ，包括其令牌值 33。但是，存储服务器记得它已经处理了具有更高令牌编号 (34) 的写入，因此它拒绝具有令牌 33 的请求。

&emsp;&emsp;请注意，这要求存储服务器主动检查令牌，并拒绝令牌已倒退的任何写入。 但一旦你掌握了窍门，这并不是特别难。 如果锁服务生成严格单调递增的令牌，则这使得锁是安全的。 例如，如果您使用 ZooKeeper 作为锁服务，则可以使用 zxid 或 znode 版本号作为 fencing 令牌，这样就处于良好状态 [ZooKeeper: Distributed Process Coordination](https://www.goodserendipity.com/asserts/zookeeper/O'Reilly.ZooKeeper.Distributed%20process%20coordination.2013.pdf)。

&emsp;&emsp;然而，这给我们带来了 Redlock 的第一个大问题：它没有任何生成栅栏令牌的工具。该算法不会产生任何保证每次客户端获取锁时都会增加的数字。这意味着即使该算法在其他方面很完美，使用起来也不安全，因为在一个客户端暂停或其数据包延迟的情况下，您无法阻止客户端之间的竞争条件。

&emsp;&emsp;对我来说，如何改变 Redlock 算法来开始生成栅栏令牌并不明显。它使用的唯一随机值不能提供所需的单调性。仅仅在一个 Redis 节点上保留一个计数器是不够的，因为该节点可能会发生故障。在多个节点上保留计数器意味着它们会不同步。您可能需要一个共识算法来生成栅栏令牌。（如果只增加一个计数器很简单就好了。）

### 利用时间达成共识

&emsp;&emsp;Redlock 无法生成防护令牌这一事实应该已经成为在正确性取决于锁的情况下不使用它的充分理由。 但还有一些进一步的问题值得讨论。

&emsp;&emsp;在学术文献中，此类算法最实用的系统模型是具有不可靠故障检测器的异步模型[Unreliable Failure Detectors for Reliable Distributed Systems](https://www.goodserendipity.com/asserts/distributed-system/Unreliable%20Failure%20Detectors%20for%20Reliable%20Distributed%20Systems.pdf)。简单来讲，这意味着算法不对时间做出任何假设：进程可能会暂停任意长度的时间，数据包可能在网络中任意延迟，时钟可能任意错误 - 但算法仍然期望做正确的事情。鉴于我们上面讨论的内容，这些都是非常合理的假设。

&emsp;&emsp;算法使用时钟的唯一目的是生成超时，以避免在节点关闭时永远等待。但超时不一定是准确的：仅仅因为请求超时，并不意味着另一个节点肯定已关闭 - 也可能是网络中存在较大延迟，或者您的本地时钟是错误的。当被用作故障检测器时，超时只是对出现问题的猜测。（如果可以的话，分布式算法将完全不需要时钟，但是共识就变得不可能[Impossibility of Distributed Consensus with One Faulty Process](https://www.goodserendipity.com/asserts/distributed-system/Impossibility%20of%20Distributed%20Consensus%20with%20One%20Faulty%20Process.pdf)。获取锁就像CAS操作需要共识[Wait-Free Synchronization](https://www.goodserendipity.com/asserts/distributed-system/Wait-Free%20Synchronization.pdf)。）

&emsp;&emsp;请注意，Redis 使用 gettimeofday 而不是[单调时钟](https://linux.die.net/man/2/clock_gettime)来确定密钥的到期时间。[gettimeofday](https://linux.die.net/man/2/gettimeofday) 的手册页明确指出，它返回的时间会受到系统时间不连续跳跃的影响 - 也就是说，它可能会突然向前跳跃几分钟，甚至向后跳跃（例如，时钟与 NTP 服务器相差太大然后由 [NTP 步进](https://www.eecis.udel.edu/~mills/ntp/html/clock.html)，或者时钟是由管理员手动调整）。因此，如果系统时钟做了奇怪的事情，很容易发生 Redis 中的密钥过期比预期快得多或慢得多的情况。

&emsp;&emsp;对于异步模型中的算法来说，这不是一个大问题：这些算法通常确保它们的安全属性始终保持不变，而无需做出任何时序假设[Consensus in the Presence of Partial Synchrony](https://www.goodserendipity.com/asserts/distributed-system/Consensus%20in%20the%20Presence%20of%20Partial%20Synchrony.pdf)。只有活性属性取决于超时或其他一些故障检测器。简单来讲，这意味着即使系统中的计时到处都是（进程暂停、网络延迟、时钟向前和向后跳跃），算法的性能可能会下降，但算法永远不会做出错误的决定。

&emsp;&emsp;然而，Redlock却不是这样的。它的安全性取决于很多计时假设：它假设所有 Redis 节点在过期之前将密钥保存大约正确的时间长度；与到期时间相比，网络延迟很小；并且进程的暂停时间比到期时间短得多。

### 错误的时序打破 Redlock

&emsp;&emsp;让我们看一些例子来证明 Redlock 对时序假设的依赖。假设系统有五个 Redis 节点（A、B、C、D 和 E）和两个客户端（1 和 2）。 如果其中一个 Redis 节点上的时钟向前跳动，会发生什么情况？

1. 客户端 1 获取节点 A、B、C 上的锁。由于网络问题，无法访问 D 和 E。
2. 节点C上的时钟向前跳跃，导致锁过期。
3. 客户端2获取节点C、D、E上的锁。由于网络问题，无法访问A和B。
4. 客户 1 和 2 现在都相信他们持有锁。

&emsp;&emsp;如果 C 在客户端 1 将锁持久化到磁盘之前崩溃并立即重新启动，则可能会发生上面类似的问题。因此，Redlock 文档建议延迟重新启动崩溃的节点，至少要延迟最长寿命锁的生存时间。但这种延迟启动再次依赖于对时间的相当准确的测量，并且如果时钟跳跃就会失败。

&emsp;&emsp;好吧，也许您认为时钟跳跃是不现实的，因为您非常有信心正确配置 NTP 以仅改变时钟。在这种情况下，让我们看一个进程暂停如何导致算法失败的示例：

1. 客户端 1 对节点 A、B、C、D、E 进行请求获取锁。
2. 当对客户端 1 的响应正在进行时，客户端 1 进入 stop-the-world GC。
3. 所有 Redis 节点上的锁都过期。
4. 客户端 2 获取节点 A、B、C、D、E 上的锁。
5. 客户端 1 完成 GC，并收到来自 Redis 节点的响应，表明它已成功获取锁（当进程暂停时，它们被保存在客户端 1 的内核网络缓冲区中(<font color="red">内核缓冲区的数据会过期丢失吗？TCP的具体实现和超时时间决定</font>)）。
6. 客户 1 和 2 现在都相信他们持有锁。

&emsp;&emsp;请注意，即使 Redis 是用 C 编写的，因此没有 GC，但这对我们没有帮助：任何客户端可能遇到 GC 暂停的系统都会出现此问题。您只能通过在客户端 2 获取锁后阻止客户端 1 在该锁下执行任何操作来确保安全，例如使用上面的隔离方法。

&emsp;&emsp;较长的网络延迟会产生与进程暂停相同的效果。这可能取决于您的 TCP 用户超时 - 如果您将超时设置为明显短于 Redis TTL，则延迟的网络数据包可能会被忽略，但我们必须详细查看 TCP 实现才能确定。此外，随着超时，我们再次回到时间测量的准确性！

### Redlock的同步假设

&emsp;&emsp;这些示例表明，仅当您采用同步系统模型（即具有以下属性的系统）时，Redlock 才能正常工作：

1. 有界网络延迟（您可以保证数据包始终在某个保证的最大延迟内到达）
2. 有界进程暂停（换句话说，硬实时约束，通常只在汽车安全气囊系统等中找到）
3. 有限时钟错误（祈祷您没有从坏的 NTP 服务器获取时间）

&emsp;&emsp;请注意，同步模型并不意味着完全同步的时钟：它意味着您假设网络延迟、进程暂停和时钟漂移有一个已知的固定上限 [Consensus in the Presence of Partial Synchrony](https://www.goodserendipity.com/asserts/distributed-system/Consensus%20in%20the%20Presence%20of%20Partial%20Synchrony.pdf)。 Redlock 假设网络延迟、进程暂停和时钟漂移漂移相对于锁的生存时间都很小；如果时序问题变得与生存时间一样大，算法就会失败。

&emsp;&emsp;在性能相当良好的数据中心环境中，大多数时间都会满足时序假设——这称为部分同步系统[Consensus in the Presence of Partial Synchrony](https://www.goodserendipity.com/asserts/distributed-system/Consensus%20in%20the%20Presence%20of%20Partial%20Synchrony.pdf)。但这足够好吗？ 一旦这些时序假设被打破，Redlock 可能会违反其安全属性，例如 在一客户端到期之前向另一客户端授予租约。如果您依赖锁保证正确性，“大多数时候”是不够的 - 您需要它始终正确。

&emsp;&emsp;有大量证据表明，对于大多数实际系统环境假设同步系统模型是不安全的，参考[The Network is Reliable](https://www.goodserendipity.com/asserts/distributed-system/The%20Network%20is%20Reliable.pdf) 和 [Downtime last Saturday](https://github.blog/2012-12-26-downtime-last-saturday/)。不断提醒自己 GitHub 90 秒数据包延迟事件。Redlock 不太可能在[Jepsen](https://aphyr.com/tags/jepsen)测试中幸存下来。

&emsp;&emsp;另一方面，为部分同步系统模型（或带有故障检测器的异步模型）设计的共识算法实际上有机会起作用。 Raft、Viewstamped Replication、Zab 和 Paxos 都属于这一类。这样的算法必须放弃所有时序假设。这很难：人们很容易认为网络延迟、进程暂停和时钟漂移比实际情况更可靠。但在分布式系统的混乱现实中，您必须非常小心您的假设。

### 结论

&emsp;&emsp;我认为 Redlock 算法是一个糟糕的选择，因为它“不伦不类”：对于效率优化锁来说，它不必要地重量级和昂贵，但对于正确性依赖于锁的情况来说，它不够安全。

&emsp;&emsp;特别是，该算法对时序和系统时钟做出了危险的假设（本质上假设同步系统具有有限的网络延迟和有限的操作执行时间），如果不满足这些假设，则会违反安全属性。此外，它缺乏生成栅栏令牌的工具（保护系统免受网络延迟或暂停进程中的长时间延迟的影响）。

&emsp;&emsp;如果您仅需要尽力而为的基础上的锁（作为效率优化，而不是为了正确性），我建议坚持使用简单的 Redis 单节点锁定算法（有条件的setnx获取锁，原子删除 delete-if-value-matches 来释放锁），并在代码中非常清楚地记录这些锁只是近似的，有时可能会失败。不必费心设置五个 Redis 节点的集群。

&emsp;&emsp;另一方面，如果您需要锁来确保正确性，请不要使用 Redlock。相反，请使用适当的共识系统，例如 ZooKeeper，可能通过实现了锁的Curator 配方之一。（至少，使用具有合理事务保证的数据库 [PostgreSQL](https://www.postgresql.org/)。）并且请在锁定下的所有资源访问上强制使用栅栏令牌。

&emsp;&emsp;正如我一开始所说，如果使用得当，Redis 是一个出色的工具。上述任何一项都不会削弱 Redis 对其预期用途的实用性。Salvatore(redis的作者) 多年来一直致力于该项目，其成功是当之无愧的。 但每种工具都有局限性，了解它们并做出相应的规划非常重要。

&emsp;&emsp;如果您想了解更多信息，我将在[Designing Data-Intensive Applications](https://www.goodserendipity.com/asserts/distributed-system/Designing%20Data-Intensive%20Applications%20-%20Martin%20Kleppmann.pdf)的第 8 章和第 9 章中更详细地解释该主题，该书现已在 O’Reilly 的早期版本中提供。（上图摘自我的书。）为了学习如何使用 ZooKeeper，我推荐 Junqueira 和 Reed 的书 [ZooKeeper: Distributed Process Coordination](https://www.goodserendipity.com/asserts/zookeeper/O'Reilly.ZooKeeper.Distributed%20process%20coordination.2013.pdf)。 为了更好地介绍分布式系统理论，我推荐 Cachin、Guerraoui 和 Rodrigues 的教科书 [Introduction to Reliable and Secure Distributed Programming](https://www.goodserendipity.com/asserts/distributed-system/Introduction%20to%20Reliable%20and%20Secure%20Distributed%20Second%20Edition%202.pdf)，[官方链接](https://www.distributedprogramming.net/)。

&emsp;&emsp;感谢 [Kyle Kingsbury](https://aphyr.com/)、[Camille Fournier](https://twitter.com/skamille)、[Camille Fournier的个人站点](https://www.camilletalk.com/)、[Flavio Junqueira](https://cncf.pravega.io/) 和 [Salvatore Sanfilippo](http://antirez.com/latest/0) 审阅本文草稿。当然，任何错误都是我的。

&emsp;&emsp;2016 年 2 月 9 日更新:Redlock 的原作者 Salvatore 发表了对本文的反驳（另见 [HN 讨论](https://news.ycombinator.com/item?id=11065933)）。他提出了一些很好的观点，但我坚持我的结论。 如果有时间，我可能会在后续文章中详细阐述，但请形成您自己的意见 - 并请查阅下面的参考文献，其中许多参考文献都经过了严格的学术同行评审（与我们的任何一篇博客文章不同）。

&emsp;&emsp;<font color="red">原文末尾有一些非常有价值的评论，可以参考。</font>

1. Martin提出的fencing token的方案，需要对提供共享资源的服务进行修改，这在现实中可行吗？
2. 根据Martin的说法，看起来，如果资源服务器实现了fencing token，它在分布式锁失效的情况下也仍然能保持资源的互斥访问。这是不是意味着分布式锁根本没有存在的意义了？
3. 资源服务器需要检查fencing token的大小，如果提供资源访问的服务也是包含多个节点的（分布式的），那么这里怎么检查才能保证fencing token在多个节点上是递增的呢？
4. Martin对于fencing token的举例中，两个fencing token到达资源服务器的顺序颠倒了（小的fencing token后到了），这时资源服务器检查出了这一问题。如果客户端1和客户端2都发生了GC pause，两个fencing token都延迟了，它们几乎同时到达了资源服务器，但保持了顺序，那么资源服务器是不是就检查不出问题了？这时对于资源的访问是不是就发生冲突了？
5. 分布式锁+fencing的方案是绝对正确的吗？能证明吗？
