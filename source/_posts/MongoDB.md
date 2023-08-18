---
title: MongoDB的基本操作
date: 2024-01-09 19:39:59
tags: MongoDB
categories: BackEnd
keywords: MongoDB
cover: https://s11.ax1x.com/2024/01/09/pFp6R7n.png
description: MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。
---
# MongoDB简介
MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供一个可扩展的高性能数据存储解决方案。MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。
![MongoDB-Logo](https://s11.ax1x.com/2024/01/09/pFp6R7n.png)
**MongoDB官网地址：** [MongoDB官网](https://www.mongodb.com)
# MongoDB概念
| SQL术语     | MongoDB术语 | 说明                                |
| ----------- | ----------- | ----------------------------------- |
| database    | database    | 数据库                              |
| table       | collection  | 数据库表/集合                       |
| row         | document    | 数据记录行/文档                     |
| column      | field       | 数据字段/域                         |
| index       | index       | 索引                                |
| table joins |             | 表连接，MongoDB不支持表连接         |
| primary key | primary key | 主键/MongoDB自动将_id字段设置为主键 |
## 数据库
MongoDB中可以建立多个数据库，默认的数据库为`db` ，该数据库存储在data目录中，每一个数据库都有自己集合和权限，不同的数据库放置在不同的文件中。
```shell
# 显示MongoDB服务器上的所有数据库
show dbs
# 示例输出
# admin 0.000GB 
# local 0.000GB
# test 0.001GB
# 显示当前所使用的数据库
db
# 指定使用某个数据库
use local
```
> MongoDB默认自带三个数据库：`admin`、`local`、`config`
* `admin` 数据库：MongoDB中主要的管理数据库，包含了MongoDB实例的所有用户和角色信息。通过该数据库可以进行用户管理、权限分配以及控制MongoDB实例的运行状态等操作。
* `local` 数据库：这是一个存储临时数据的地方，例如复制同步过程中的操作日志。在这个数据库中，可以找到一些关键的集合(collection)，例如`startup_log`和`oplog.rws` ，它们记录了MongoDB实例的启动日志和操作日志。
* `config` 数据库：包含了MongoDB的分片信息，用于支持分片集群。在一个分片集群中，每个节点都有一个`config` 数据库，用于存储集群的各种配置和元数据。
**数据库的名字应是满足以下条件的任意UTF-8字符串：**
1. 不能是空字符串
2. 不得含有' '（空格)、.、$、/、\和\0 (空字符)
3. 应全部小写
4. 最多64字节
## 文档(Document)
文档是一组键值(key-value)对(即BSON)。MongoDB的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是MongoDB的特点。
```json
{"site":"www.runoob.com", "name":"菜鸟教程"}
```

 **RDBMS 与 MongoDB 对应的术语：**
 
| RDBMS | MongoDB |
| ---- | ---- |
| 数据库 | 数据库 |
| 表格 | 集合 |
| 行 | 文档 |
| 列 | 字段 |
| 表联合 | 嵌入文档 |
| 主键 | 主键（MongoDB提供了key为_id） |
> 注意：
1. 文档中的键/值对是**有序**的。
2. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
3. MongoDB**区分类型**和**大小写**。
4. MongoDB的文档**不能有重复的键**。
5. 文档的**键是字符串**。除了少数例外情况，键可以使用任意UTF-8字符。

**文档键的命名规范：**
- 键不能含有\0 (空字符)。
- .和$有特别的意义，只有在特定环境下才能使用。
- 以下划线"_"开头的键是保留的(不是严格要求的)。

## 集合
集合就是MongoDB文档组，类似于RDBMS（关系型数据库管理系统：Retational Database Management System）中的表格。集合存在于数据库中，没有固定的结构；集合可以插入不同格式和类型的数据，但通常情况下插入集合的数据都会有一定的关联性。
```json
{"site":"www.baidu.com"}
{"site":"www.google.com","name":"Google"}
{"site":"www.runoob.com","name":"菜鸟教程","num":5}
```
> 当第一个文档插入时，集合就会被创建。
### 合法的集合名
- 集合名不能是空字符串""。
- 集合名不能含有\0字符（空字符)。
- 集合名不能以"system."开头，这是为系统集合保留的前缀。
- 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。
### capped collections
**capped collections**是固定大小的collecting有很高的性能以及队列过期的特性（过期按照插入的顺序），有点类似于“RRD”。**capped collections** 是高性能自动的维护对象的插入顺序。非常适合类似于记录日志的功能，和标准的collection不同，必须要显示的创建一个capped collections，指定一个 collection 的大小，单位是字节。collection 的数据存储空间值提前分配的。
```shell
db.createCollection("mycoll", {capped:true, size:100000})
```
- 在 capped collection 中，你能添加新的对象。
- 能进行更新，然而，对象不会增加存储空间。如果增加，更新就会失败 。
- 使用 Capped Collection 不能删除一个文档，可以使用 drop() 方法删除 collection 所有的行。
- 删除之后，你必须显式的重新创建这个 collection。
- 在32bit机器中，capped collection 最大存储为 1e9( 1X109)个字节。
## 元数据
数据库的信息是存储在集合中。它们使用系统的命名空间：
```text
dbname.system.*
```
在MongoDB数据库中名字空间 `<dbname>.system.*`是包含多种系统信息的特殊集合(Collection):

|集合命名空间|描述|
|--------------|-----|
|dbname.system.namespaces|列出所有名字空间。|
|dbname.system.indexes|列出所有索引。|
|dbname.system.profile|包含数据库概要(profile)信息。|
|dbname.system.users|列出所有可访问数据库的用户。|
|dbname.local.sources|包含复制对端（slave）的服务器信息和状态。|

**对于修改系统集合中的对象有如下限制：** 
1. 在{{system.indexes}}插入数据，可以创建索引。但除此之外该表信息是不可变的(特殊的drop index命令将自动更新相关信息)。
2. {{system.users}}是可修改的。 {{system.profile}}是可删除的。
## MongoDB 数据类型
**MongoDB中常用的几种数据类型。**

| 数据类型 | 描述 |
| ---- | ---- |
| String | 字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。 |
| Integer | 整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。 |
| Boolean | 布尔值。用于存储布尔值（真/假）。 |
| Double | 双精度浮点值。用于存储浮点值。 |
| Min/Max keys | 将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。 |
| Array | 用于将数组或列表或多个值存储为一个键。 |
| Timestamp | 时间戳。记录文档修改或添加的具体时间。 |
| Object | 用于内嵌文档。 |
| Null | 用于创建空值。 |
| Symbol | 符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。 |
| Date | 日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。 |
| Object ID | 对象 ID。用于创建文档的 ID。 |
| Binary Data | 二进制数据。用于存储二进制数据。 |
| Code | 代码类型。用于在文档中存储 JavaScript 代码。 |
| Regular expression | 正则表达式类型。用于存储正则表达式。 |
### 重要的数据类型
#### ObjectId
ObjectId 类似唯一主键，可以很快的去生成和排序，包含 12 bytes，含义是：
- 前 4 个字节表示创建 **unix** 时间戳,格林尼治时间 **UTC** 时间，比北京时间晚了 8 个小时
- 接下来的 3 个字节是机器标识码
- 紧接的两个字节由进程 id 组成 PID
- 最后三个字节是随机数
![](https://www.runoob.com/wp-content/uploads/2013/10/2875754375-5a19268f0fd9b_articlex.jpeg)
MongoDB 中存储的文档必须有一个 _id 键。这个键的值可以是任何类型的，默认是个 ObjectId 对象

由于 ObjectId 中保存了创建的时间戳，所以你不需要为你的文档保存时间戳字段，你可以通过 getTimestamp 函数来获取文档的创建时间:
```bash
> var newObject = ObjectId()
> newObject.getTimestamp()
ISODate("2017-11-25T07:21:10Z")
```
ObjectId 转为字符串
```bash
> newObject.str
5a1919e63df83ce79df8b38f
```
#### 字符串

**BSON 字符串都是 UTF-8 编码。**
#### 时间戳
BSON 有一个特殊的时间戳类型用于 MongoDB 内部使用，与普通的 日期 类型不相关。 时间戳值是一个 64 位的值。其中：
- 前32位是一个 time_t 值（与Unix新纪元相差的秒数）
- 后32位是在某秒中操作的一个递增的`序数`
在单个 mongod 实例中，时间戳值通常是唯一的。在复制集中， oplog 有一个 ts 字段。这个字段中的值使用BSON时间戳表示了操作时间。
> BSON 时间戳类型主要用于 MongoDB 内部使用。在大多数情况下的应用开发中，你可以使用 BSON 日期类型。
#### 日期
表示当前距离 Unix新纪元（1970年1月1日）的毫秒数。日期类型是有符号的, 负数表示 1970 年之前的日期。
```bash
> var mydate1 = new Date()     //格林尼治时间
> mydate1
ISODate("2018-03-04T14:58:51.233Z")
> typeof mydate1
object
> var mydate2 = ISODate() //格林尼治时间
> mydate2
ISODate("2018-03-04T15:00:45.479Z")
> typeof mydate2
object
```
这样创建的时间是日期类型，可以使用 JS 中的 Date 类型的方法。返回一个时间类型的字符串：
```bash
> var mydate1str = mydate1.toString()
> mydate1str
Sun Mar 04 2018 14:58:51 GMT+0000 (UTC) 
> typeof mydate1str
string
> Date()
Sun Mar 04 2018 15:02:59 GMT+0000 (UTC)   
```
# MongoDB连接
**连接MongoDB的URL语法**
`mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]`
- **mongodb://** 这是固定的格式，必须要指定。
- **username:password@** 可选项，如果设置，在连接数据库服务器之后，驱动都会尝试登录这个数据库
- **host1** 必须的指定至少一个host, host1 是这个URI唯一要填写的。它指定了要连接服务器的地址。如果要连接复制集，请指定多个主机地址。
- **portX** 可选的指定端口，如果不填，默认为27017
- **/database** 如果指定username:password@，连接并验证登录指定数据库。若不指定，默认打开 test 数据库。
- **?options** 是连接选项。如果不使用/database，则前面需要加上/。所有连接选项都是键值对name=value，键值对之间通过&或;（分号）隔开
**连接URL案例：** `mongodb://admin:123456@localhost/test` 
**options选项：**

|选项|描述|
|---|---|
|replicaSet=name|验证replica set的名称。 Impliesconnect=replicaSet.|
|slaveOk=true\|false|- true:在connect=direct模式下，驱动会连接第一台机器，即使这台服务器不是主。在connect=replicaSet模式下，驱动会发送所有的写请求到主并且把读取操作分布在其他从服务器。<br>- false: 在 connect=direct模式下，驱动会自动找寻主服务器. 在connect=replicaSet 模式下，驱动仅仅连接主服务器，并且所有的读写命令都连接到主服务器。|
|safe=true\|false|- true: 在执行更新操作之后，驱动都会发送getLastError命令来确保更新成功。(还要参考 wtimeoutMS).<br><br>false: 在每次更新之后，驱动不会发送getLastError来确保更新成功。|
|w=n|驱动添加 { w : n } 到getLastError命令. 应用于safe=true。|
|wtimeoutMS=ms|驱动添加 { wtimeout : ms } 到 getlasterror 命令. 应用于 safe=true.|
|fsync=true\|false|- true: 驱动添加 { fsync : true } 到 getlasterror 命令.应用于 safe=true.<br>- false: 驱动不会添加到getLastError命令中。|
|journal=true\|false|如果设置为 true, 同步到 journal (在提交到数据库前写入到实体中). 应用于 safe=true|
|connectTimeoutMS=ms|可以打开连接的时间。|
|socketTimeoutMS=ms|发送和接受sockets的时间。|
# MongoDB创建数据库
MongoDB创建数据库的语法格式：
```shell
use DATABASE_NAME
```
> 创建一个数据库

```shell
use runoob
# 查看数据库
db
runoob
# 插入一条数据
db.runoob.insertOne({"name":"lyj"})
# 查看所有数据库，发现新建的数据库已存在
show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
lyj     0.000GB
```
>如果现在查看所有数据库，会发现当前新建的数据库并不存在，只有往数据库中插入数据之后，才能查看到
# MongoDB删除数据库
MongoDB删除数据库的语法格式：
```shell
db.dropDatabase()
```
>删除数据库具体操作

```shell
# 选中需要删除的数据库
use lyj
# 执行删除数据库的命令
db.dropDatabase()
# 查看所有数据库，发现新建的数据库已删除
show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
```
# MongoDB创建集合
MongoDB创建集合的语法格式：
```shell
db.createCollection(name, options)
```
**options具体参数**

|字段|类型|描述|
|---|---|---|
|capped|布尔|（可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。  <br>**当该值为 true 时，必须指定 size 参数。**|
|autoIndexId|布尔|3.2 之后不再支持该参数。（可选）如为 true，自动在 _id 字段创建索引。默认为 false。|
|size|数值|（可选）为固定集合指定一个最大值，即字节数。  <br>**如果 capped 为 true，也需要指定该字段。**|
|max|数值|（可选）指定固定集合中包|
> 创建集合

```shell
# 创建数据库
use test
# 创建集合
db.createCollection("lyj")
# 查看已有集合show collections/show tables两个命令都是查询已有集合
show collections
lyj
```
# MongoDB删除集合
MongoDB删除集合的语法格式：
```shell
db.collection.drop()
```
> 删除集合

```shell
# 删除名叫lyj的集合
db.lyj.drop()
# 查看是否删除成功
show tables
```
# MongoDB插入文档
MongoDB插入文档的语法格式：
```shell
db.COLLECTION_NAME.insert(document)
或
db.COLLECTION_NAME.save(document)
```
- save()：如果 _id 主键存在则更新数据，如果不存在就插入数据。该方法新版本中已废弃，可以使用 **db.collection.insertOne()** 或 **db.collection.replaceOne()** 来代替。
- insert(): 若插入的数据主键已经存在，则会抛 **org.springframework.dao.DuplicateKeyException** 异常，提示主键重复，不保存当前数据。
**3.2 版本之后新增了 db.collection.insertOne() 和 db.collection.insertMany()。**
`db.collection.insertOne()` 语法格式：
```shell
db.collection.insertOne(
   <document>,
   {
      writeConcern: <document>
   }
)
```
`db.collection.insertMany()` 语法格式：
```shell
db.collection.insertMany(
   [ <document 1> , <document 2>, ... ],
   {
      writeConcern: <document>,
      ordered: <boolean>
   }
)
```
参数说明：
- document：要写入的文档。
- writeConcern：写入策略，默认为 1，即要求确认写操作，0 是不要求。
- ordered：指定是否按顺序写入，默认 true，按顺序写入
> `insertOne()` 表示插入一条数据，`insertMany()` 表示插入多条数据

插入文档
```shell
# 该命令会往test数据的lyj集合中插入文档
db.lyj.insert({title: 'MongoDB 教程', 
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
})
# 查看已插入的文档
db.lyj.find()
```
# MongoDB更新文档
MongoDB更新文档的语法格式：
```shell
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```
**参数说明：**
- **query** : update的查询条件，类似sql update查询内where后面的。
- **update** : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
- **upsert** : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- **multi** : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- **writeConcern** :可选，抛出异常的级别。
> 更新文档

```shell
db.lyj.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}})
```
# MongoDB删除文档
MongoDB删除数据库的语法格式：
```shell
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```
**参数说明：**
- **query** :（可选）删除的文档的条件。
- **justOne** : （可选）如果设为 true 或 1，则只删除一个文档，如果不设置该参数，或使用默认值 false，则删除所有匹配条件的文档。
- **writeConcern** :（可选）抛出异常的级别。
> 删除文档

```shell
db.lyj.remove({'title':'MongoDB 教程'})
```
# MongoDB查询文档
MongoDB查询文档的语法格式：
```shell
db.collection.find(query, projection)
```
- **query** ：可选，使用查询操作符指定查询条件
- **projection** ：可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）。
> pretty() 方法以格式化的方式来显示所有文档`db.lyj.find().pretty()`

## MongoDB 与 RDBMS Where 语句比较
|操作|格式|范例|RDBMS中的类似语句|
|---|---|---|---|
|等于|`{<key>:<value>`}|`db.col.find({"by":"菜鸟教程"}).pretty()`|`where by = '菜鸟教程'`|
|小于|`{<key>:{$lt:<value>}}`|`db.col.find({"likes":{$lt:50}}).pretty()`|`where likes < 50`|
|小于或等于|`{<key>:{$lte:<value>}}`|`db.col.find({"likes":{$lte:50}}).pretty()`|`where likes <= 50`|
|大于|`{<key>:{$gt:<value>}}`|`db.col.find({"likes":{$gt:50}}).pretty()`|`where likes > 50`|
|大于或等于|`{<key>:{$gte:<value>}}`|`db.col.find({"likes":{$gte:50}}).pretty()`|`where likes >= 50`|
|不等于|`{<key>:{$ne:<value>}}`|`db.col.find({"likes":{$ne:50}}).pretty()`|`where likes != 50`|
## MongoDB的逻辑运算符
```shell
# and运算符
db.lyj.find({key1:value1, key2:value2}).pretty()
# 案例
db.lyj.find({"by":"菜鸟教程", "title":"MongoDB 教程"}).pretty()
{
        "_id" : ObjectId("56063f17ade2f21f36b03133"),
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
# or运算符
>db.col.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty()
# 案例
db.lyj.find({$or:[{"by":"菜鸟教程"},{"title": "MongoDB 教程"}]}).pretty()
{
        "_id" : ObjectId("56063f17ade2f21f36b03133"),
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
# and和or一起使用
db.lyj.find({"likes": {$gt:50}, $or: [{"by": "菜鸟教程"},{"title": "MongoDB 教程"}]}).pretty()
{
        "_id" : ObjectId("56063f17ade2f21f36b03133"),
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
```
# MongoDB $type操作符
`$type`操作符是基于BSON类型来检索集合中匹配的数据类型，并返回结果。
**MongoDB 中可以使用的类型如下表所示：**

|**类型**|**数字**|**备注**|
|---|---|---|
|Double|1||
|String|2||
|Object|3||
|Array|4||
|Binary data|5||
|Undefined|6|已废弃。|
|Object id|7||
|Boolean|8||
|Date|9||
|Null|10||
|Regular Expression|11||
|JavaScript|13||
|Symbol|14||
|JavaScript (with scope)|15||
|32-bit integer|16||
|Timestamp|17||
|64-bit integer|18||
|Min key|255|Query with -1.|
|Max key|127||
**案列**
```shell
# 想获取 "col" 集合中 title 为 String 的数据，这两个的含义都是一样的
db.col.find({"title" : {$type : 2}})
db.col.find({"title" : {$type : 'string'}})
```
# MongoDB Limit和Skip方法
MongoDB的Limit()方法的语法格式：
```shell
db.COLLECTION_NAME.find().limit(NUMBER)
```
> Limit()方法

```shell
db.lyj.find({},{"title":lyj,_id:0}).limit(2)
```
MongoDB的Skip()方法的语法格式：
```shell
db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
```
> Skip()方法

```shell
db.lyj.find({},{"title":1,_id:0}).limit(1).skip(1)
```
# MongoDB sort方法
在 MongoDB 中使用 sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。
sort()方法的语法格式：
```shell
db.COLLECTION_NAME.find().sort({KEY:1})
```
> sort()方法

```shell
db.lyj.find({},{"title":1,_id:0}).sort({"likes":-1})
```
# MongoDB 索引
索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。
这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可能要花费几十秒甚至几分钟，这对网站的性能是非常致命的。
索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库表中一列或多列的值进行排序的一种结构
> MongoDB使用 createIndex() 方法来创建索引。

createIndex()方法基本语法格式：
```shell
db.collection.createIndex(keys, options)
# 实例
db.lyj.createIndex({"title":1})
```
createIndex() 接收可选参数，可选参数列表：

|Parameter|Type|Description|
|---|---|---|
|background|Boolean|建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引，即增加 "background" 可选参数。 "background" 默认值为**false**。|
|unique|Boolean|建立的索引是否唯一。指定为true创建唯一索引。默认值为**false**.|
|name|string|索引的名称。如果未指定，MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称。|
|dropDups|Boolean|3.0+版本已废弃。在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为 **false**.|
|sparse|Boolean|对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为true的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 **false**.|
|expireAfterSeconds|integer|指定一个以秒为单位的数值，完成 TTL设定，设定集合的生存时间。|
|v|index version|索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。|
|weights|document|索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重。|
|default_language|string|对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语|
|language_override|string|对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language.|
# MongoDB aggregate() 方法
`aggregate()` 方法在MongoDB中的作用是聚合
aggregate() 方法的基本语法格式：
```shell
db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)
```
聚合的表达式:

|表达式|描述|实例|
|---|---|---|
|$sum|计算总和。|db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}])|
|$avg|计算平均值|db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}])|
|$min|获取集合中所有文档对应值得最小值。|db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}])|
|$max|获取集合中所有文档对应值得最大值。|db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}])|
|$push|将值加入一个数组中，不会判断是否有重复的值。|db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}])|
|$addToSet|将值加入一个数组中，会判断是否有重复的值，若相同的值在数组中已经存在了，则不加入。|db.mycol.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}])|
|$first|根据资源文档的排序获取第一个文档数据。|db.mycol.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}])|
|$last|根据资源文档的排序获取最后一个文档数据|db.mycol.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}])|
## 管道
管道在Unix和Linux中一般用于将当前命令的输出结果作为下一个命令的参数。
MongoDB的聚合管道将MongoDB文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的。
表达式：处理输入文档并输出。表达式是无状态的，只能用于计算当前聚合管道的文档，不能处理其它的文档。
聚合框架中常用的操作：
- $project：修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。
- $match：用于过滤数据，只输出符合条件的文档。$match使用MongoDB的标准查询操作。
- $limit：用来限制MongoDB聚合管道返回的文档数。
- $skip：在聚合管道中跳过指定数量的文档，并返回余下的文档。
- $unwind：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。
- $group：将集合中的文档分组，可用于统计结果。
- $sort：将输入文档排序后输出。
- $geoNear：输出接近某一地理位置的有序文档。
### 管道操作符实例
```shell
# $project实例
db.article.aggregate(
    { $project : {
        title : 1 ,
        author : 1 ,
    }}
 );
# $match实例
db.articles.aggregate( [
                        { $match : { score : { $gt : 70, $lte : 90 } } },
                        { $group: { _id: null, count: { $sum: 1 } } }
                       ] );
# $skip实例
db.article.aggregate(
    { $skip : 5 });
```