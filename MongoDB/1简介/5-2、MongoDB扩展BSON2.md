##### 消歧

​    接下来讨论MongoDB扩展JSON(v2)。

​    对 [`mongosh`](https://docs.mongodb.com/mongodb-shell/#mongodb-binary-bin.mongosh)支持的数据类型，参考 [mongosh Data Types](https://docs.mongodb.com/mongodb-shell/reference/data-types/)。

​    对遗留的 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) 脚本，参考 [Data Types in the Legacy `mongo` Shell](https://docs.mongodb.com/manual/core/shell-types/)。

​    [JSON](https://docs.mongodb.com/manual/reference/glossary/#std-term-JSON) 仅可以直接表示 [BSON](https://docs.mongodb.com/manual/reference/glossary/#std-term-BSON)支持的类型的子集。为了保存类型信息，MongoDB对JSON格式添加如下的扩展：

​    1、规范模式

​        一个强调类型保存的、以读和操作性为代价的字符串格式。也就是，从规范模式转换成BSON通常会保存类型信息，除了特定指明场景。

​    2、松散模式

​        一个强调读和互操作性、以类型保存为代价的字符串格式。也就是，从松散模式转换为BSON格式会丢失类型信息。

​    两种格式都符合 [JSON RFC](http://www.json.org/)，并且可以被各种各样的MongoDB驱动和工具解析。

MongoDB扩展JSON v2 用例

###### 驱动

如下驱动使用扩展JSON v2

C		Java		PHPC

C++	Node	 Python

Go	  Perl		Scala

C#和Ruby使用传统的MongoDB扩展JSON v1，参考 [MongoDB Extended JSON (v1)](https://docs.mongodb.com/manual/reference/mongodb-extended-json-v1/).

###### MongoDB数据库工具

4.2版本开始

| Binary                                                       | Changes                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`bsondump`](https://docs.mongodb.com/database-tools/bsondump/#mongodb-binary-bin.bsondump) | 使用扩展JSON v2.0 (规范模式)格式                             |
| [`mongodump`](https://docs.mongodb.com/database-tools/mongodump/#mongodb-binary-bin.mongodump) | 元数据使用扩展JSON v2.0 (规范模式)格式，要求[`mongorestore`](https://docs.mongodb.com/database-tools/mongorestore/#mongodb-binary-bin.mongorestore) 4.2版本或者更新的支持扩展JSON v2.0(规范模式或者松散模式)格式。<br /><br />通常，使用对应版本的 [`mongodump`](https://docs.mongodb.com/database-tools/mongodump/#mongodb-binary-bin.mongodump) 和 [`mongorestore`](https://docs.mongodb.com/database-tools/mongorestore/#mongodb-binary-bin.mongorestore).也就是说，保存使用指定版本的 [`mongodump`](https://docs.mongodb.com/database-tools/mongodump/#mongodb-binary-bin.mongodump)创建的数据文件，使用对应版本的 [`mongorestore`](https://docs.mongodb.com/database-tools/mongorestore/#mongodb-binary-bin.mongorestore) |
| [`mongoexport`](https://docs.mongodb.com/database-tools/mongoexport/#mongodb-binary-bin.mongoexport) | 默认使用扩展JSON v2.0 (松散模式)创建导出数据<br /><br />如果使用`--jsonFormat`创建导出数据，使用扩展JSON v2.0 (规范模式) |
| [`mongoimport`](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport) | 默认导入数据用扩展JSON v2.0 (松散模式或者规范模式)<br />指定 `--legacy` 选项可以识别扩展JSON v1.0 格式的数据<br />通常， [`mongoexport`](https://docs.mongodb.com/database-tools/mongoexport/#mongodb-binary-bin.mongoexport) 和 [`mongoimport`](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport) 版本应该匹配。也就是说，导入 [`mongoexport`](https://docs.mongodb.com/database-tools/mongoexport/#mongodb-binary-bin.mongoexport)创建的数据，应该使用对应版本的 [`mongoimport`](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport) |

###### BSON 数据类型和相关的展示

​    下面列出了一些常见的BSON数据类型和相关的规范模式和松散模式展示。

|                                                              |                                                              |                                                              |                                                              |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`Array`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Array)<br />[`Binary`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Binary)<br />[`Date`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Date) | [`Decimal128`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Decimal128)<br />[`Document`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Document)<br />[`Double`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Double) | [`Int32`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Int32)<br />[`Int64`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Int64)<br />[`MaxKey`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-MaxKey) | [`MinKey`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-MinKey)<br />[`ObjectId`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-ObjectId)<br />[`Regular Expression`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Regular-Expression)<br />[`Timestamp`](https://docs.mongodb.com/manual/reference/mongodb-extended-json/#mongodb-bsontype-Timestamp) |

[完整列表]( https://github.com/mongodb/specifications/blob/master/source/extended-json.rst#conversion-table)

###### 数组

| Canonical        | Relaxed               |
| :--------------- | :-------------------- |
| `[ <elements> ]` | `<Same as Canonical>` |

数组元素使用扩展JSON。指定空数组，省略内容 `[ ]`。

###### 二进制

| Canonical                                                    | Relaxed               |
| :----------------------------------------------------------- | :-------------------- |
| `{ "$binary":   {      "base64": "<payload>",      "subType": "<t>"   }}` | `<Same as Canonical>` |

​    "<payload>"	base64编码(空格作为"=")的payload 字符串

​    "<t>"   一个或者两个字符的16进制字符串，对应一个BSON二进制子类型，参考[扩展JSON文档](http://bsonspec.org/spec.html)子类型。

###### 日期

​    1970到9999之间的日期

| Canonical                                | Relaxed                                    |
| :--------------------------------------- | :----------------------------------------- |
| `{"$date": {"$numberLong": "<millis>"}}` | `{"$date": "<ISO-8601 Date/Time Format>"}` |

​    1970以前或者9999以后的日期

| Canonical                                | Relaxed               |
| :--------------------------------------- | :-------------------- |
| `{"$date": {"$numberLong": "<millis>"}}` | `<Same as Canonical>` |

"<millis>"	一个64位有符号整型的字符串。值表示原点相关的毫秒数。

"<ISO-8601 Date/Time Format>" 

​					[ISO-8601 Internet Date/Time Format](https://tools.ietf.org/html/rfc3339#section-5.6)日期作为字符串。

​					日期或者时间的最大时间精度是毫秒

​							秒数部分有3个十进制位置，如果这部分是非0的

​							否则，秒数部分应该被省略，如果是0的话

###### Decimal128

3.4版本开始

| Canonical                          | Relaxed               |
| :--------------------------------- | :-------------------- |
| `{ "$numberDecimal": "<number>" }` | `<Same as Canonical>` |

"<number>"	[高精度小数](https://github.com/mongodb/specifications/blob/master/source/bson-decimal128/decimal128.rst)作为字符串。

###### 文档

| Canonical       | Relaxed               |
| :-------------- | :-------------------- |
| `{ <content> }` | `<Same as Canonical>` |

<content>	使用扩展JSON的键值对，制定一个空的文档，省略内容即可 `{ }`.

###### Double

有限数字

| Canonical                                | Relaxed                |
| :--------------------------------------- | :--------------------- |
| `{"$numberDouble": "<decimal string>" }` | `<non-integer number>` |

无限数字

| Canonical                                            | Relaxed               |
| :--------------------------------------------------- | :-------------------- |
| `{"$numberDouble": <"Infinity"|"-Infinity"|"NaN"> }` | `<Same as Canonical>` |

"<decimal string>"	一个64位比特有符号浮点数作为字符串

<non-integer number>	非整型数字。整形数字被解析为整型，而不是double。

###### Int64

| Canonical                       | Relaxed     |
| :------------------------------ | :---------- |
| `{ "$numberLong": "<number>" }` | `<integer>` |

"<number>"	一个64比特有符号整型作为字符串

<integer>	一个64比特有符号整型

###### Int64

| Canonical                      | Relaxed     |
| :----------------------------- | :---------- |
| `{ "$numberInt": "<number>" }` | `<integer>` |

"<number>"	一个32比特有符号整型的字符串

<integer>	一个32比特的有符号整型

###### MaxKey

| Canonical          | Relaxed               |
| :----------------- | :-------------------- |
| `{ "$maxKey": 1 }` | `<Same as Canonical>` |

MaxKey BSON数据类型比较高于所有其他类型，参考 [Comparison/Sort Order](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#std-label-faq-dev-compare-order-for-BSON-types) 。

###### MinKey

| Canonical          | Relaxed               |
| :----------------- | :-------------------- |
| `{ "$minKey": 1 }` | `<Same as Canonical>` |

MinKey BSON类型比较低于所有其他类型，参考 [Comparison/Sort Order](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#std-label-faq-dev-compare-order-for-BSON-types) 。

###### ObjectId

| Canonical                        | Relaxed               |
| :------------------------------- | :-------------------- |
| `{ "$oid": "<ObjectId bytes>" }` | `<Same as Canonical>` |

"<ObjectId bytes>"	一个24字符，大端法的十六进制字符串

###### Regular Expression

| Canonical                                                    | Relaxed               |
| :----------------------------------------------------------- | :-------------------- |
| `{ "$regularExpression":   {      "pattern": "<regexPattern>",      "options": "<options>"  }}` | `<Same as Canonical>` |

"<regexPattern>"	一个正则表达式字符串。这个字符串可以包含合法JSON字符和保留的双引号(`"`)，但是不能包含保留的斜杠(`/`) 字符。

"<options>"	指定BSON正则选项 ('g', 'i', 'm' and 's')或者空字符串 `""`.

​							转换为这种表示的时候，除了('g', 'i', 'm' and 's') 的选项会被丢弃。

选项必须是字母顺序排列的

###### 时间戳

| Canonical                              | Relaxed               |
| :------------------------------------- | :-------------------- |
| `{"$timestamp": {"t": <t>, "i": <i>}}` | `<Same as Canonical>` |

<t>	表示原点以后的正整型秒数

<i>	自增的正整型



示例

| Example Field Name | Canonical Format                                      | Relaxed Format                                        |
| :----------------- | :---------------------------------------------------- | :---------------------------------------------------- |
| "_id:"             | {"$oid":"5d505646cf6d4fe581014ab2"}                   | {"$oid":"5d505646cf6d4fe581014ab2"}                   |
| "arrayField":      | ["hello",{"$numberInt":"10"}]                         | ["hello",10]                                          |
| "dateField":       | {"$date":{"$numberLong":"1565546054692"}}             | {"$date":"2019-08-11T17:54:14.692Z"}                  |
| "dateBefore1970":  | {"$date":{"$numberLong":"-1577923200000"}}            | {"$date":{"$numberLong":"-1577923200000"}}            |
| "decimal128Field": | {"$numberDecimal":"10.99"}                            | {"$numberDecimal":"10.99"}                            |
| "documentField":   | {"a":"hello"}                                         | {"a":"hello"}                                         |
| "doubleField":     | {"$numberDouble":"10.5"}                              | 10.5                                                  |
| "infiniteNumber"   | {"$numberDouble":"Infinity"}                          | {"$numberDouble":"Infinity"}                          |
| "int32field":      | {"$numberInt":"10"}                                   | 10                                                    |
| "int64Field":      | {"$numberLong":"50"}                                  | 50                                                    |
| "minKeyField":     | {"$minKey":1}                                         | {"$minKey":1}                                         |
| "maxKeyField":     | {"$maxKey":1}                                         | {"$maxKey":1}                                         |
| "regexField":      | {"$regularExpression":{"pattern":"^H","options":"i"}} | {"$regularExpression":{"pattern":"^H","options":"i"}} |
| "timestampField":  | {"$timestamp":{"t":1565545664,"i":1}}                 | {"$timestamp":{"t":1565545664,"i":1}}                 |