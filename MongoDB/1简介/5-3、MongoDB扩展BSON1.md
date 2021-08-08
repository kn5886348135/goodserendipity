消歧

接下来讨论MongoDB扩展JSON v1(传统的扩展JSON)，MongoDB扩展JSON v2参考 [MongoDB Extended JSON (v2)](https://docs.mongodb.com/manual/reference/mongodb-extended-json/).

对于 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) 支持的数据类型，参考 [mongosh Data Types](https://docs.mongodb.com/mongodb-shell/reference/data-types/).

对于传统的 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) shell支持的数据类型，参考 [Data Types in the Legacy `mongo` Shell](https://docs.mongodb.com/manual/core/shell-types/).

[JSON](https://docs.mongodb.com/manual/reference/glossary/#std-term-JSON) 可以仅代表 [BSON](https://docs.mongodb.com/manual/reference/glossary/#std-term-BSON) 支持的类型的子集。为了保存类型信息，MongoDB对JSON格式添加了如下的扩展。

1、严格模式。严格模式表示符合 [JSON RFC](http://www.json.org/) 的BSON类型。任何JSON解析器可以解析严格模式表示的键值对，但是只有MongoDB内部的JSON解析器可以识别这个格式转达的类型信息。

2、`mongo` Shell模式。MongoDB内部的JSON解析器和 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) 脚本可以解析这种模式。

各种数据类型使用的表示法依赖JSON被解析的上下文。

###### MongoDB扩展JSON v1和MongoDB驱动

C#和Ruby使用扩展JSON v1(传统的).

其他的驱动，参考 [MongoDB Extended JSON (v2)](https://docs.mongodb.com/manual/reference/mongodb-extended-json/).

###### 解析器和支持的格式

严格模式输入

下面的是可以解析严格模式表示法，识别类型信息。

- [`mongoimport`](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport) 4.0及更早版本
- 各种MongoDB工具的 `--query` 选项
- [MongoDB Compass](https://www.mongodb.com/products/compass)

 `mongo`  shell模式输入

下面的是可以解析 `mongod` shell 模式表示法，识别类型信息。

- [`mongoimport`](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport) 4.0版本及更早版本
- 各种MongoDB工具的 `--query` 选项
- [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) shell

严格模式输出

4.2版本以前， [`mongoexport`](https://docs.mongodb.com/database-tools/mongoexport/#mongodb-binary-bin.mongoexport) 用MongoDB扩展JSON v1严格模式输出数据。

`mongo` shell模式输出 

4.2版本以前， [`bsondump`](https://docs.mongodb.com/database-tools/bsondump/#mongodb-binary-bin.bsondump) 使用 `mongo` shell模式输出

###### BSON 数据类型和相关的表示法

下面展示了使用严格模式和 `mongo` shell 模式的BSON数据类型和相关表示法。

###### Binary

| Strict Mode                                  | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :------------------------------------------- | :----------------------------------------------------------- |
| `{ "$binary": "<bindata>", "$type": "<t>" }` | `BinData ( <t>, <bindata> )`                                 |

<bindata>	一个二进制字符串的base64编码

<t>				表示数据类型的单个字节。在严格模式下是十六进制字符串，shell模式下是一个整型。参考[扩展BSON文档](http://bsonspec.org/spec.html)

###### 日期

| Strict Mode             | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :---------------------- | :----------------------------------------------------------- |
| `{ "$date": "<date>" }` | `new Date ( <date> )`                                        |

严格模式下，<date>是一个有强制时区属性的ISO-8601的日期格式，模板为`YYYY-MM-DDTHH:mm:ss.mmm<+/-Offset>`.

shell模式下，<date>是一个64位有符号整型、给出UTC原点以来的毫秒数的JSON表示。

###### 时间戳

| Strict Mode                                | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :----------------------------------------- | :----------------------------------------------------------- |
| `{ "$timestamp": { "t": <t>, "i": <i> } }` | `Timestamp( <t>, <i> )`                                      |

<t>是UTC原点以来的秒数的32位无符号整型的JSON表示。

<i>是自增的、32位无符号整型。

###### 正则表达式

| Strict Mode                                          | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :--------------------------------------------------- | :----------------------------------------------------------- |
| `{ "$regex": "<sRegex>", "$options": "<sOptions>" }` | `/<jRegex>/<jOptions>`                                       |

<sRegex>是一个合法的JSON字符串。

<jRegex>是一个包含合法JSON字符和未转义的双引号(`"`)字符的字符串，但是不包含未转义的前斜杠 (`/`) 字符。

<sOptions>是一个包含通过字母表中的字母表示的正则表达式选项的字符串。

<jOptions>是一个只包含 'g', 'i', 'm' and 's' (added in v1.9)字符的字符串。因为`JavaScript` 和 `mongo Shell` 表示法支持一个有限范围的选项，在转换成这种表示法的时候，任何不相容的选项将会被抛弃。

###### OID

| Strict Mode          | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :------------------- | :----------------------------------------------------------- |
| `{ "$oid": "<id>" }` | `ObjectId( "<id>" )`                                         |

<id>是一个24位字符的16进制字符串。

###### DB Reference

| Strict Mode                           | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :------------------------------------ | :----------------------------------------------------------- |
| `{ "$ref": "<name>", "$id": "<id>" }` | `DBRef("<name>", "<id>")`                                    |

<name>是一个合法的JSON字符的字符串。

<id>是一个任意合法的扩展JSON类型。

###### 未定义类型

| Strict Mode              | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :----------------------- | :----------------------------------------------------------- |
| `{ "$undefined": true }` | `undefined`                                                  |

在查询文档中不能使用未定义类型。对于如下的 `people` 集合
```
db.people.insert( { name : "Sally", age : undefined } )
```
如下的查询将返回一个错误
```
db.people.find( { age : undefined } )
db.people.find( { age : { $gte : undefined } } )
```
但是可以使用 `$type` 来查询 `age` 属性值是 `undefined` 的文档。

###### MinKey

| Strict Mode        | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :----------------- | :----------------------------------------------------------- |
| `{ "$minKey": 1 }` | `MinKey`                                                     |

MinKey BSON类型比所有其他类型都要小，参考[Comparison/Sort Order](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#std-label-faq-dev-compare-order-for-BSON-types) 。

###### MaxKey

| Strict Mode        | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :----------------- | :----------------------------------------------------------- |
| `{ "$maxKey": 1 }` | `MaxKey`                                                     |

MaxKey BSON类型比其他所有类型都要大，参考[Comparison/Sort Order](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#std-label-faq-dev-compare-order-for-BSON-types) 。

###### NumberLong

| Strict Mode                     | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :------------------------------ | :----------------------------------------------------------- |
| `{ "$numberLong": "<number>" }` | `NumberLong( "<number>" )`                                   |

`NumberLong`是一个64位有符号整型。必须包含引号，或者他能被解释为一个浮点型数据，结果就是准确性的丧失。

例如，将 `9223372036854775807` 作为 `NumberLong` 插入，有双引号和没有双引号。
```
db.json.insert( { longQuoted : NumberLong("9223372036854775807") } )
db.json.insert( { longUnQuoted : NumberLong(9223372036854775807) } )
```
当你检索这个文档的时候， `longUnQuoted` 的值已经改变了，但是 `longQuoted` 保留了它的准确性。
```
db.json.find(){ "_id" : ObjectId("54ee1f2d33335326d70987df"), "longQuoted" :NumberLong("9223372036854775807") }
{ "_id" : ObjectId("54ee1f7433335326d70987e0"), "longUnQuoted" : NumberLong("-9223372036854775808") }
```
###### NumberDecimal

| Strict Mode                        | [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) Shell Mode |
| :--------------------------------- | :----------------------------------------------------------- |
| `{ "$numberDecimal": "<number>" }` | `NumberDecimal( "<number>" )`                                |

`NumberDecimal` 是一个[高精度小数](https://github.com/mongodb/specifications/blob/master/source/bson-decimal128/decimal128.rst)。必须包含双引号，否则输入的数字会被当做double，导致数据丢失。

例如，将 `123.40` 作为 `NumberDecimal` 插入，有双引号和没有双引号。
```
db.json.insert( { decimalQuoted : NumberDecimal("123.40") } )
db.json.insert( { decimalUnQuoted : NumberDecimal(123.40) } )
```
当你检索这个文档的时候， `decimalUnQuoted` 的值已经改变，但是 `decimalQuoted` 保持了指定的精度。
```
db.json.find()
{ "_id" : ObjectId("596f88b7b613bb04f80a1ea9"), "decimalQuoted" : NumberDecimal("123.40") }
{ "_id" : ObjectId("596f88c9b613bb04f80a1eaa"), "decimalUnQuoted" : NumberDecimal("123.400000000000") }
```
