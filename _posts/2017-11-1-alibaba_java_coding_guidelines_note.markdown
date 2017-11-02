---
layout: post
title:  "阿里巴巴java开发手册学习笔记"
date:   2017-10-30 18:48:30 +0800
categories: picture-mouse
tags: 
- java
- study
---



## POJO

POJO (Plain Old Java Object) , 这种叫法是Martin Fowler、Rebecca Parsons和Josh MacKenzie在2000年的一次演讲的时候提出来的。按照Martin Fowler的解释是“Plain Old Java Object”，从字面上翻译为“纯洁老式的java对象”，但大家都使用“简单java对象”来称呼它。

有一些private的参数作为对象的属性，然后针对每一个参数定义get和set方法访问的接口。没有从任何类继承、也没有实现任何接口，更没有被其它框架侵入的java对象

POJO格式用于数据的临时传递，它只能装载数据，作为数据存储的载体，不具有逻辑处理的能力

~~~java
public class User {  
  
    private String name;  
    private int age;  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
  
    public int getAge() {  
        return age;  
    }  
  
    public void setAge(int age) {  
        this.age = age;  
    }  
  
}
~~~

### PO

Persistent Object 持久化对象，将物理数据的一种对象表示的承载，可以简化对象数据与物理数据之间的转换，不受任何业务的干涉

如果持久层是关系型数据库，那么数据表中的每个字段分别对应PO的属性

### DTO

Data Transfer Object 数据传输对象，可以泛指服务层与展示层之间的数据传输对象

### VO

Value Object 值对象，由业务逻辑使用，为数据提供一个承载，VO的属性根据当前业务的不同而不同，对应当前业务逻辑所需要的数据的名称

业务逻辑层与数据持久层独立开，业务层则不会受到数据持久层的影响，业务层关心的则只是业务逻辑的处理

如果没有数据持久层，则PO/VO可以是同一个对象

### BO

Business Object 业务对象, 主要作用是吧业务逻辑封装为一个对象，对象可以包括一个或多个其它对象

如一个简历中有教育经历，工作经历，社会关系等

### DO



## JavaBean

一种JAVA语言写成的可重用组件。JavaBean符合一定规范编写的Java类，不是一种技术，而是一种规范。大家针对这种规范，总结了很多开发技巧、工具函数。符合这种规范的类，可以被其它的程序员或者框架使用。它的方法命名，构造及行为必须符合特定的约定

1. 所有属性为private
2. 这个类必须有一个公共的缺省构造函数
3. 这个类的属性使用getter和setter来访问，其他方法遵从标准命名规范
4. 这个类应是可序列化的。实现serializable接口

JavaBean中往往会封装一些简单逻辑，javabean中可以有其它的方法



## 库

- 一方库
- 二方库
- 三方库



## 相关链接

- [阿里巴巴Java开发手册&IDE规约插件](https://github.com/alibaba/p3c)
- [JAVABEAN EJB POJO区别](http://www.cnblogs.com/yw-ah/p/5795751.html)
- [浅析VO、DTO、DO、PO的概念、区别和用处](http://www.blogjava.net/johnnylzb/archive/2010/05/27/321968.html)