---
layout: post
title: 软件设计模式概述
date: 2019-8-28 17:41
category: MongoDB
tags: [MongoDB, 数据库, NOSQL]
description: 一些关于MongoDB系统设计的一些问题：比如如何保证数据的完整和一致性等问题。
---



### MongoDB数据库深入了解的一些问题



#### 设计思想

MongoDB抛弃了关联的思路来设计表结构，提倡的设计思路可能是建立一个客户表,在表中包含业务需要的尽可能多的数据信息,这样最终会产生一些冗余信息,但是在 NoSQL 的世界里是提倡这样做的。



#### MongoDB如何保证数据的完整性和一致性的？

​		和关系型数据库一样，MongoDB也是通过锁机制来保证数据的完整性和一致性的。MongoDB 利用读写锁来支持并发操作，读锁可以共享，写锁具有排它性。当一个读锁存在时，其他读操作也可以用这个读锁；但是当一个写锁存在时,其他任何读写操作都不能共享这把锁，当一个读和写都等待一个锁时， MongoDB 将优先分配锁给写操作。
​		从版本2.2开始，MongoDB 在每一个数据库上实现锁的粒度，当然对于某些极少数操作，实例上的全局锁仍然存在，锁粒度的降低能够提高系统的并发性。成熟的关系数据库锁的粒度更低,它可以在表中的某一行上,即“行级锁”。常见的操作和产生的锁类型如下：查询读锁，增删改写锁，默认前台创建索引产生写锁，aggregate 操作产生读锁等。

