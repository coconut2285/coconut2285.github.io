---
layout: post
title: MongoDB基础
date: 2018-8-17 20:00
category: MongoDB
tags: [MongoDB, NoSQL、数据库]
description: MongoDB是由C++编写的面向文档的NoSQL数据库。本文给出MongoDB的一些基础。
---
我一直认为学习一门语言最重要的基础，对基础的理解，有一些示例Demo来验证学到的知识和想法是一个很好的学习方式，最近在跟着阿里大学学习MongoDB，在此整理一下学到的一些基础，希望在这个过程中可以学到更多新鲜的知识。

## 简介

Mongodb 是一款流行的NOSQL数据库，使用的数据类型 BSON（类似 JSON，JSON的二进制形式）。

- 模式灵活，更以处理扁平格式的数据(例如：CSV和分层数据等)
- 部署灵活
- 适用于大数据

#### MongoDB服务器
MongoDB的运行需要启动一个服务器(mongo)和一个客户端(mongod)。

```
mogod.exe 					启动客户端
	--dbpath				制定数据库存放的路径
	--logpath				指定日志存放路径
mongo.exe 					启动服务器
    --port                  指定端口号，默认27017端口
```
示例：
```
mongod.exe --dbpath "C:\\MongoDB\data\db" --logpath "C:\\MongoDB\data\log\mongo.log"
mongo.exe --port 27017
```
### 数据库状态查询、新建、删除

#### 数据库状态查询

```mongodb
show dbs										查询数据库
show tables/show collections					查看数据库中的表
db.stats()										查看数据库所有的状态信息
db.version										查看数据库的版本
db.serverStatus()								查询服务器状态
db.serverStatus().storageEngine					查看存储引擎
show profile
```

#### 新建数据库

```
use hello		进入hello数据库，如果不存在则创建
```

#### 删除数据库

```
db.dropDatabase()
```

### 数据插入、查询、过滤、删除、修改、投影、去重、聚合分析

#### 数据插入、查询、过滤基础

```
use Hello										创建/切换Hello数据库
db.users.save({name:"Ada",age:24})				向当前数据库users表中插入数据
db.users.insert({name:"Ada",age:20})			向当前数据库users表中插入数据
db.users.find() 								查询当前数据库users表中所有数据
db.users.find({age:24})							查询age=24的数据
db.users.find({age:{$lt:24}})					查找age<24
db.users.find({age:{$lte:24}})					查找age<=24
db.users.find({age:{$gt:24}})					查找age>24
db.users.find({age:{$gte:24}})					查找>=24
db.users.find({age:{$gt:18,$lt:24}})			18<age< 24
db.users.find({name:/.*a.*/})					a字母所在位置不限定
db.users.find({name:/^a.*/})					a字母开头
db.users.find({name:/.*a$/})					a字母结尾
db.users.find({age:{$in:[24,32]}})				查询age=24和32的
db.users.find({age:{$lte:24}, name:"Ada"})		age<=24 name=Ada
db.users.count()								查询数量
db.users.find({age:24}).count()					查询age=24的数量
db.users.find().sort({age:1})					按照age升序排列
db.users.find().sort({age:-1})					按照age降序排列
db.users.find().sort({age:-1}).pretty()			优化结果集显示
```

#### 删除数据

```
db.users.remove({"age":23})    					删除age=23的数据
db.users.remove({})								删除users集合中所有的数据					
```

#### 更改数据

```
db.collection.update(criteria,objNew,upsert,multi)

参数说明：
:param criteria:	查询条件
:param objNew:		update对象和一些更新操作符
:param upsert:		若不存在update记录，是否插入objNew文档，true为插入，false为不插入，默认为false。
:param multi:		默认是false，只更新找到的第一条记录；如果为true，把按条件查询出来的记录全部更新。
```

使用示例：

```
db.users.update({"age":32},{$set:{"name":"Lucy"}}) 	将从users集合中age=32的第一条记录name更改为Lucy
db.users.update({"age":32},{$set:{"name":"Lucy"}}， false, true)	将从users集合中age=32的所有记录name更改为Lucy
db.users.update({"age":31},{$set:{"name":"Lucy"}}， true)	    将从users集合中age=31的所有记录name更改为Lucy，如果不存在age=31的在插入{"age":31， "name":"Lucy"}
```

#### 文档嵌套和嵌套文档查询

```
db.users.insert({name:"Lily", age:18, items:[{title:"java", count:1}, {title:"Python", count:2}]})	
db.users.insert({name:"Lucy", age:16, address:{country:"China", city:"hanzhou"}})					嵌套数组文档
db.users.find({"address.country":"china"})		嵌套查询
db.users.find({'items.0.title':"java"})			第0个元素的title=java的
db.users.find({'items.title':"java"})			存在元素的title=java的
```

#### 投影

```
db.users.find({age:{$lt:24}}, {name:1})			显示age<24的name信息
db.users.find({age:{$lt:24}}, {name:1, age:1})	显示age<24的name和age信息
db.users.find({age:{$lt:24}}, {name:0})			不显示age<24的name的其他信息
```
#### 去重

```
db.users.distinct("name")						返回不重复的name
db.users.distinct("age",{"address.city":"hangzhou"})	返回hangzhou不重复的age信息
```

#### 聚合分析
##### group查询

```
db.collection.group({ key, reduce, initial[, keyf] [, cond] [, finalize] })
	key					分组关键字
	reduce 				初始化变量，有两个参数：当前文档和结果文档。
	cond 				过滤条件
	finalize
```

- 1 统计各个年龄段的用户数

```
db.users.group(
	{
		key:{age:1},
		reduce:function(curr,result){
			result.total += 1;
		},
		initial:{total:0}
	}
)
```

- 2 统计age小于18的各个年纪的用户数（不同年龄段用户统计）

```
db.users.group(
	{
		key:{age:1},
		cond:{age:{$lt:18}},
		reduce:function(curr,result){
			result.total += 1;
		},
		initial:{total:0}
	}
)
```

- 3 统计所有age小于18岁的用户

```
db.users.group(
	{
		key:{},
		cond:{age:{$lt:18}},
		reduce:function(curr,result){
			result.total += 1;
		},
		initial:{total:0}
	}
)
```

### 数据导入

#### mongoimport

mongoimport可以将指定格式文件(json或csv)导入指定集合中。 

```
mongoimport --help			查看帮助文件

mongoimport
	-d						使用的数据库
    -c						使用的集合
    --file					文件名称
```

示例：

```
//将example.json导入example数据库的hello集合中
mongoimport -d example -c hello --file example.json
```

### 正则运算

MongoDB支持perl兼容的正则表达式。

```
db.users.find({"name" : {"$regex": "[Ll]ily"}})			查找name中包含Lily或lily的信息
db.users.find({"name" : {"$regex": "[Ll]ily | Lucy"}})	查找name中包含Lily或lily或Lucy的信息
```

### in运算符

```
db.users.find({"age" : {"$in" : [16, 17, 24]}})			查找age等于16、17或24的用户信息
```

### all运算符

```
db.users.find({"model" : {"$all" : [16, 17, 24]}})		查找model包含16、17、24的用户信息
```

