MongoDB的安装



操作系统MongoDB分为社区版和企业版，本文只考虑社区版的Debian，使用apt包管理器方式安装。

MongoDB5.0社区版支持64位Debian，x86_64架构，具体[支持的平台](https://docs.mongodb.com/manual/administration/production-notes/#std-label-prod-notes-supported-platforms) 。

生产环境部署MongoDB，考虑[生产环境说明](https://docs.mongodb.com/manual/administration/production-notes/)。

在Debian系统安装MongoDB社区版，本文使用官方的 `mongodb-org` 安装包，这是MongoDB公司维护和支持的。官方的 `mongodb-org` 安装包一直包含最新的MongoDB版本，可以从它自己专用的仓库获取。

Debian提供的`mongodb` 安装包不是MongoDB公司维护的，并且和官方的`mongodb-org`安装包冲突。如果你已经在Debian系统安装了`mongodb`安装包，在使用本文当前必须卸载`mongodb` 安装包。

完整官方安装包列表参考 [MongoDB Community Edition Packages](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-debian/#std-label-debian-package-content) 。

 Debian10

参照[官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-debian/)执行

1、导入公钥

wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -

正常返回OK，如果返回错误，需要安装gnupg

sudo apt-get install gnupg

2、创建MongoDB的source文件

echo "deb http://repo.mongodb.org/apt/debian buster/mongodb-org/4.4 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list

3、更新源

sudo apt-get update

4、安装MongoDB

安装最新稳定版本 sudo apt-get install -y mongodb-org

虽然你可以指定任何可用的MongoDB版本，当一个新的版本可用的时候，`apt-get` 将会升级安装包。为了阻止意料之外的更新，可以将安装包固定在当前安装的版本。
```
echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-database hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-org-shell hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
```
5、运行MongoDB社区版

ulimit 注意事项

​    多数类Unix操作系统都会限制进程可以使用的资源。这些限制可能给MongoDB操作带来负面影响，应该被调整。建议设置参考 [UNIX `ulimit` Settings](https://docs.mongodb.com/manual/reference/ulimit/) 。MongoDB4.4开始，如果打开的文件数量的`ulimit`值低于64000，会生成一个启动错误。

Directories

MongoDB实例默认储存数据在 `/var/lib/mongodb`，储存日志在 `/var/log/mongodb`。

如果使用包管理器安装，这些默认的目录在安装的时候被创建。如果下载安装包手动安装，可以使用运行MongoDB的用户执行`mkdir -p <directory>` 或者 `sudo mkdir -p <directory>`创建目录。MongoDB默认使用`mongodb` 用户运行。如果改变了运行MongoDB的用户，必须同时修改 `/var/lib/mongodb` 和 `/var/log/mongodb` 目录，使用户可以访问这些目录。指定不同的日志文件目录和数据文件目录，可以编辑 `/etc/mongod.conf`文件的 [`systemLog.path`](https://docs.mongodb.com/manual/reference/configuration-options/#mongodb-setting-systemLog.path) 和 [`storage.dbPath`](https://docs.mongodb.com/manual/reference/configuration-options/#mongodb-setting-storage.dbPath) 设置，并且保证运行MongoDB的用户有权限访问这些目录。

本文接下来运行MongoDB社区版的步骤，基于官方的`mongodb-org`安装包，而不是Debian提供的非官方安装包`mongodb`，并且使用的是默认设置。

初始化系统

运行和管理`mongod`进程，将会使用操作系统内置的 [init system](https://docs.mongodb.com/manual/reference/glossary/#std-term-init-system)。

如果不确定你的操作系统使用的是systemd还是System V init，可以执行

ps --no-headers -o comm 1

结果显示systemd使用systemctl命令，最近版本的Linux操作系统都是使用systemctl

结果显示init使用service命令，省略System V init操作步骤。

6、开启MongoDB

sudo systemctl start mongod

如果启动失败，提示

Failed to start mongod.service: Unit mongod.service not found.

执行sudo systemctl daemon-reload

7、验证MongoDB启动成功

sudo systemctl status mongod

开启开机启动

sudo systemctl enable mongod

停止MongoDB

sudo systemctl stop mongod

重启MongoDB

sudo systemctl restart mongod

可以通过查看日志输出文件`/var/log/mongodb/mongod.log`，查看进程状态的错误或者重要信息。

8、使用MongoDB

在当前主机上开启一个`mongosh`会话作为`mongod`。不带任何命令行参数运行`mongosh`连接一个运行在本机，默认端口为27017的`mongod`。使用`mongosh`连接其他主机或者端口的`mongod`实例，参考 [mongosh documentation](https://docs.mongodb.com/mongodb-shell/)。MongoDB使用文档 [Getting Started Guides](https://docs.mongodb.com/manual/tutorial/getting-started/#std-label-getting-started) ，驱动文档 [Start Developing with MongoDB](https://api.mongodb.com/)。

9、配置远程连接

修改/etc/mongo.conf文件的bindIp，将127.0.0.1改为0.0.0.0，如果在后面继续添加客户端ip会报错

no suitable servers found (serverSelectionTimeOutMS)

IP绑定的更多信息参考 [IP Binding](https://docs.mongodb.com/manual/core/security-mongodb-configuration/)。

10、MongoDB分为mongod、mongos进程

11、MongoDB卸载步骤略。

12、MongoDB社区版安装包

MongoDB社区版安装包可以从它自己专用的仓库获取，包含很多官方支持的安装包

| Package Name           | Description                                                  |
| :--------------------- | :----------------------------------------------------------- |
| `mongodb-org`          | A `metapackage` that automatically installs the component packages listed below. |
| `mongodb-org-database` | A `metapackage` that automatically installs the component packages listed below.Package NameDescription`mongodb-org-server`Contains the [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#mongodb-binary-bin.mongod) daemon, associated init script, and a [configuration file](https://docs.mongodb.com/manual/reference/configuration-options/#std-label-conf-file) (`/etc/mongod.conf`). You can use the initialization script to start [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#mongodb-binary-bin.mongod) with the configuration file. For details, see the "Run MongoDB Community Edition" section, above.`mongodb-org-mongos`Contains the [`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#mongodb-binary-bin.mongos) daemon.`mongodb-org-shell`Contains the legacy [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) shell. |
| `mongodb-mongosh`      | Contains the MongoDB Shell ([`mongosh`](https://docs.mongodb.com/mongodb-shell/#mongodb-binary-bin.mongosh)). |
| `mongodb-org-tools`    | A `metapackage` that automatically installs the component packages listed below:Package NameDescription`mongodb-database-tools`Contains the following MongoDB database tools:[`mongodump`](https://docs.mongodb.com/database-tools/mongodump/#mongodb-binary-bin.mongodump)[`mongorestore`](https://docs.mongodb.com/database-tools/mongorestore/#mongodb-binary-bin.mongorestore)[`bsondump`](https://docs.mongodb.com/database-tools/bsondump/#mongodb-binary-bin.bsondump)[`mongoimport`](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport)[`mongoexport`](https://docs.mongodb.com/database-tools/mongoexport/#mongodb-binary-bin.mongoexport)[`mongostat`](https://docs.mongodb.com/database-tools/mongostat/#mongodb-binary-bin.mongostat)[`mongotop`](https://docs.mongodb.com/database-tools/mongotop/#mongodb-binary-bin.mongotop)[`mongofiles`](https://docs.mongodb.com/database-tools/mongofiles/#mongodb-binary-bin.mongofiles)`mongodb-org-database-tools-extra`Contains the [`install_compass`](https://docs.mongodb.com/manual/reference/program/install_compass/#std-label-install-compass) script |