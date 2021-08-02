[BSON](https://docs.mongodb.com/manual/reference/glossary/#std-term-BSON) 是一个MongoDB中用来存储文档和远程调用的二进制序列化格式。BSON规范参考 [bsonspec.org](http://bsonspec.org/) 。

每一个BSON类型同时有整型和字符串标志符，在下表列出：

<table cellspacing="0" cellpadding="0" class="leafygreen-ui-bfo1fy"><thead><tr class="" data-testid="leafygreen-ui-header-row"><th role="columnheader" scope="col" aria-sort="none" class="leafygreen-ui-tvkscu"><div class="leafygreen-ui-177up3s"><span class="leafygreen-ui-1l7ulzy">Type</span></div></th><th role="columnheader" scope="col" aria-sort="none" class="leafygreen-ui-tvkscu"><div class="leafygreen-ui-177up3s"><span class="leafygreen-ui-1l7ulzy">Number</span></div></th><th role="columnheader" scope="col" aria-sort="none" class="leafygreen-ui-tvkscu"><div class="leafygreen-ui-177up3s"><span class="leafygreen-ui-1l7ulzy">Alias</span></div></th><th role="columnheader" scope="col" aria-sort="none" class="leafygreen-ui-tvkscu"><div class="leafygreen-ui-177up3s"><span class="leafygreen-ui-1l7ulzy">Notes</span></div></th></tr></thead><tbody><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Double</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>1</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"double"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>String</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>2</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"string"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Object</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>3</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"object"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Array</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>4</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"array"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Binary data</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>5</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"binData"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Undefined</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>6</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"undefined"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Deprecated.</span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>ObjectId</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>7</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"objectId"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Boolean</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>8</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"bool"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Date</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>9</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"date"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Null</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>10</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"null"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Regular Expression</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>11</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"regex"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>DBPointer</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>12</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"dbPointer"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Deprecated.</span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>JavaScript</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>13</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"javascript"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Symbol</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>14</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"symbol"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Deprecated.</span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>JavaScript code with scope</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>15</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"javascriptWithScope"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Deprecated in MongoDB 4.4.</span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>32-bit integer</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>16</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"int"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Timestamp</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>17</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"timestamp"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>64-bit integer</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>18</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"long"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Decimal128</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>19</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"decimal"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>New in version 3.4.</span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Min key</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>-1</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"minKey"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr><tr role="row" class="leafygreen-ui-55pr4m" aria-disabled="false"><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>Max key</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>127</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span>"maxKey"</span></div></td><td class="leafygreen-ui-chd2rj"><div class="leafygreen-ui-1sg2lsz" data-leafygreen-ui="td-inner-div"><span></span></div></td></tr></tbody></table>

-  [`$type`](https://docs.mongodb.com/manual/reference/operator/query/type/#mongodb-query-op.-type) 操作符支持这些值的BSON类型查询。
-  [`$type`](https://docs.mongodb.com/manual/reference/operator/query/type/#mongodb-query-op.-type) 也支持 `number` 匹配 integer, decimal, double, and long BSON 类型的别名。
-  [`$type`](https://docs.mongodb.com/manual/reference/operator/aggregation/type/#mongodb-expression-exp.-type) 聚合操作返回参数的 BSON 类型。
- 从4.4版本开始如果参数是BSON integer, decimal, double, or long， [`$isNumber`](https://docs.mongodb.com/manual/reference/operator/aggregation/isNumber/#mongodb-expression-exp.-isNumber)  聚合操作返回 `true` 。

​    如果要判断一个属性的类型，参考 [Check Types in the `mongo` Shell](https://docs.mongodb.com/manual/core/shell-types/#std-label-check-types-in-shell) 。

​    如果要把BSON转换为JSON，参考 [Extended JSON](https://docs.mongodb.com/manual/reference/mongodb-extended-json/) 。

​    下面的部分描述了特定BSON类型的注意事项。

###### ObjectId

​    ObjectId是小巧的，可能唯一的，可以快速生成的，有序的。ObjectId值是12字节长度，组成是：

- 4字节时间戳从Unix纪元开始以秒计量的，表示ObjectId的创建时间。
- 5字节随机值。
- 3字节的随机值初始化的递增计数器。

​    但是BSON格式是低位优先的，时间戳和计数器值是高位优先的，最重要的字节在字节序列的第一位出现。

​    在MongoDB里面， 各个存储在集合中的文档必须有一个作为主键( [primary key](https://docs.mongodb.com/manual/reference/glossary/#std-term-primary-key))的  [_id](https://docs.mongodb.com/manual/reference/glossary/#std-term-_id)  属性。如果一个插入文档省略了`_id`属性，MongoDB驱动会为这个`_id`属性自动生成一个[ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#std-label-objectid)。

​    这也同样适用于通过 [upsert: true](https://docs.mongodb.com/manual/reference/method/db.collection.update/#std-label-upsert-parameter) 更新的插入文档。

​    MongoDB客户端应该添加一个具有唯一性的ObjectId值的`_id`属性。为`_id`属性使用ObjectIds提供如下好处：

- 在 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) 脚本中，可以使用 [`ObjectId.getTimestamp()`](https://docs.mongodb.com/manual/reference/method/ObjectId.getTimestamp/#mongodb-method-ObjectId.getTimestamp) 方法访问 `ObjectId` 的创建时间。
- 存储一个保存了`ObjectId`的 `_id` 属性约等于按照创建时间排序。

​    [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#std-label-objectid) 值应该随着时间递增，但是他们不是必须单调的。因为：

- 只包含一秒钟的时间分辨率，所以在同一秒创建的 [ObjectId](https://docs.mongodb.com/manual/reference/bson-types/#std-label-objectid) 值不保证顺序。
- 客户端生成，可能有不同的系统时钟。

​    参考[`ObjectId()`](https://docs.mongodb.com/manual/reference/method/ObjectId/#mongodb-method-ObjectId)

###### 字符串

​    BSON字符串是UTF-8编码。通常，各种编程语言的驱动在序列化和反序列化BSON的时候，将各自语言的字符串格式转换为UTF-8编码。这使在BSON字符串中简单地存储最国际通用的字符变的可能(给定的使用UTF-8字符集的字符串，在字符串上排序理应是正确的。但是，因为内部的排序([`sort()`](https://docs.mongodb.com/manual/reference/method/cursor.sort/#mongodb-method-cursor.sort) )使用C++的 `strcmp` API，排序命令对某些字符的处理可能是不正确的)。另外，MongoDB的 [`$regex`](https://docs.mongodb.com/manual/reference/operator/query/regex/#mongodb-query-op.-regex) 要求在正则表达式中支持UTF-8。

​    给定使用UTF-8字符集的字符串，在字符串上使用 [`sort()`](https://docs.mongodb.com/manual/reference/method/cursor.sort/#mongodb-method-cursor.sort) 将会是正确的。但是，因为内部排序使用C++的strcmp API，排序可能对一些字符串处理地不正确。

###### 时间戳

​    BSON有一个特殊的MongoDB内部使用的时间戳类型，和常规的 [Date](https://docs.mongodb.com/manual/reference/bson-types/#std-label-document-bson-type-date) 类型并不相关。这个内部时间戳类型是一个64位比特值

​    最高的32位比特是一个time_t值(从Unix起点开始以秒计)

​    最低的32位比特是一个对给定秒数操作的自增序列。

​    因为BSON格式是低位优先的，所以低位比特先存储，mongod实例总是在所有平台对比ordinal之前的time_t值，无视字节顺序。

​    单个mongod实例内，时间戳的值总是唯一的。

​    在副本实例中， [oplog](https://docs.mongodb.com/manual/reference/glossary/#std-term-oplog) 有一个 ts 属性。这个属性中的值使用一个BSON时间戳的值，反映了操作时间。

​    MongoDB内部使用的BSON时间戳类型，在应用开发的大多数时候，开发者倾向于使用BSON的 [Date](https://docs.mongodb.com/manual/reference/bson-types/#std-label-document-bson-type-date) 类型。

​    当插入一个包含顶层属性是空的时间戳值的文档的时候，MongoDB使用当前时间戳代替空的时间戳值，除了 _id 属性自己包含一个空的时间戳值，他总是会被原样插入，不会被替换。
```
插入带有空时间戳的文档
db.test.insertOne( { ts: new Timestamp() } );
```

​    运行 [`db.test.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#mongodb-method-db.collection.find) 将会返回如下结果
```
{ "_id" : ObjectId("542c2b97bac0595474108b48"), "ts" : Timestamp(1412180887, 1) }
```

日期

​    BSON日期类型是一个64位比特的整型值，表示从Unix原点(1970年1月1日)开始的毫秒数。这个结果可以表示大概包括过去和将来的290百万年范围的日期。

​    [BSON官方指南]([official BSON specification](http://bsonspec.org/#/specification))将BSON日期类型作为UTC时间.

​    BSON日期类型是有符号的，负值表示1970年之前的日期。(2.0版本以前，日期被错误地解释为无符号整型，这会影响排序、范围查询和日期属性上的索引。因为索引在升级的时候不会被重新创建，如果你使用较早版本的日期类型上创建了索引，并且1970年以前的日期对你的应用比较重要，请重新索引)

在 [`mongosh`](https://docs.mongodb.com/mongodb-shell/#mongodb-binary-bin.mongosh) 中用 `new Date()` 创建日期
```
var mydate1 = new Date()
```

在 [`mongosh`](https://docs.mongodb.com/mongodb-shell/#mongodb-binary-bin.mongosh) 中用 `ISODate()` 创建日期
```
var mydate2 = ISODate()
```

将日期作为字符串返回
```
mydate1.toString()
```

返回日期的月份部分，月份是从0开始索引的，所以1月是0：
```
mydate1.getMonth()
```

