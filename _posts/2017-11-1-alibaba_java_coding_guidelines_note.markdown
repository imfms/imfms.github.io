---
layout: post
title:  "阿里巴巴java开发手册学习手记"
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

### PO

Persistent Object 持久化对象，将物理数据的一种对象表示的承载，可以简化对象数据与物理数据之间的转换，不受任何业务的干涉

如果持久层是关系型数据库，那么数据表中的每个字段分别对应PO的属性

### DTO

Data Transfer Object 数据传输对象，可以泛指服务层与展示层之间的数据传输对象

### VO

View Object 视图对象，某个页面、视图组件数据的承载

### BO

Business Object 业务对象, 主要作用是把业务逻辑封装为一个对象，对象可以包括一个或多个其它对象

如一个简历中有教育经历，工作经历，社会关系等

## JavaBean

一种JAVA语言写成的可重用组件。JavaBean符合一定规范编写的Java类，不是一种技术，而是一种规范。大家针对这种规范，总结了很多开发技巧、工具函数。符合这种规范的类，可以被其它的程序员或者框架使用。它的方法命名，构造及行为必须符合特定的约定

1. 所有属性为private
2. 这个类必须有一个公共的缺省构造函数
3. 这个类的属性使用getter和setter来访问，其他方法遵从标准命名规范
4. 这个类应是可序列化的。实现serializable接口

JavaBean中往往会封装一些简单逻辑，javabean中可以有其它的方法

### DO

Domain Object 领域对象, 从现实世界中抽象出来的有形或无形的业务实体

特定领域的业务逻辑对象，可以拥有业务方法

如人类，存在嘴巴、手脚，拥有吃饭、跑步行为



![](/img/post/vo_dto_do_dao_po.png)



> 引用 [PO BO VO DTO POJO DAO DO这些Java中的概念分别指一些什么？ - Knight王的回答](https://www.zhihu.com/question/39651928/answer/87536000)
>
> 当你业务足够简单时，一个POJO 也完全当做PO BO DTO VO 看
> 比如有个用户类 只有 name 以及 phone 
> 对于数据库层面也就两列，业务层面，传输，和前台展示时 都只有这两项
>
> 他们区别开来的例子：
> 1 、还是用户类  name phone 加了个password。
> 那么你后端的PO属性也是这3个，一般数据库里这个表有几个字段你的PO就有多少属性，但是传输到前台或者展现时，我们不应该把password 密码这种东西也一起传过去，所以他们的DTO VO 就还是 name + phone 
> po : name phone password
> dto : name phone
> vo : name phone
>
> 2、现在又加了一个 枚举的状态位 status 表示用户的一些特殊状态，前台不会直接显示，可能会根据这个状态产生后续的操作
> po : name phone password status
> dto : name phone status
> vo : name phone
>
> 3、BO ，一个用户下面 肯定会关联很多其他的表
> 比如用户设置 用户信息等，那么这个BO 下 不但有用户本身的一些属性，还包含了用户设置 和用户信息这两个类



## 依赖库

- 一方库
  本项目中的依赖
- 二方库
  公司内部其他项目提供的依赖
- 三方库
  其他组织，公司等来自第三方的依赖



## 相关链接

- [阿里巴巴Java开发手册&IDE规约插件](https://github.com/alibaba/p3c)
- [JAVABEAN EJB POJO区别](http://www.cnblogs.com/yw-ah/p/5795751.html)
- [领域驱动设计系列文章（1）——通过现实例子显示领域驱动设计的威力](http://www.blogjava.net/johnnylzb/archive/2010/05/15/321057.html)
- [领域驱动设计系列文章（2）——浅析VO、DTO、DO、PO的概念、区别和用处](http://www.blogjava.net/johnnylzb/archive/2010/05/27/321968.html)