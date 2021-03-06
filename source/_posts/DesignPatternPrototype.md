---
layout: post
title: 设计模式：原型模式
date: 2019-2-13 21：06
category: 设计模式
tags: [设计模式]
description: 简单介绍了原型设计模式，并提供了利用原型设计模式实现的简历制作相关程序实现。
---

​	

### 原型模式

​	**原型设计(prototype)用原型实例指定创建对象的种类，并通过拷贝这些原型创建新的对象。**原型其实就是从一个对象再创建另外一个可定制的对象，动态地获取对象运行状态，而且不需知道任何创建的细节。[原型模式：制作简历实现](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E5%8E%9F%E5%9E%8B%E6%A8%A1%E5%BC%8F/%E5%8E%9F%E5%9E%8B%E6%A8%A1%E5%BC%8F-%E5%88%B6%E4%BD%9C%E7%AE%80%E5%8E%86.cpp)

实际应用示例：

- [Prorobuf Message](https://www.ibm.com/developerworks/cn/linux/l-cn-gpb/index.html)



知乎上看到的对原型模式的意义的解释:

> Prototype 的意义在于，你拿到一个 Base* ，它指向某个 Derived 对象，你想克隆出 Derived 对象，但代码中不写出 Derived 的具体类型，因为有很多派生类，这种情况下你用构造函数是搞不定的，type-switch 是 bad smells 。
>
> 另外，这里考虑 virtual 的性能损失是主次不分，构造对象需要分配内存，这开销比一次虚函数调用大多了。

#### 优点

- 一般在初始化不发生变化时，克隆是最好的办法。既可以隐藏对象初始化细节，而且可以大大提高性能。

