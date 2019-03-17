---
layout: post
title:  "Android-Binder机制 学习手记"
subtitle: "Binder模糊了进程边界，淡化了进程间通信过程，整个系统仿佛运行于同一个面向对象的程序之中"
date:   2019-03-17 15:35:00 +1200
categories: android
tags: 
- android
- develop
- study
- note
---

> *doc.imf.ms/timeflow/201903-5*



Android应用可以包含组件：Activity、Broadcast、Service、ContentProvider。不同应用在不同进程下。但不同应用中的不同组件却是可以互相通信的，并且这之间也没什么太大的阻碍。忙碌期间不由得就在想：“各进程间独立，不共享内存资源，对其他进程也没什么控制权，那他们是怎么通信的呢？”平时里耳濡目染的听说底层其实是一个叫Binder的机制实现的，空余时间里我试着去了解这一机制。



交集：AIDL
----------

提到Android间多进程通信，第一个直接想到的技术是AIDL,
一种Android提供的RPC机制。之前有朋友做开发需要使用该技术调用第三方提供的功能，当时自己也凑了凑热闹，同时过程中也给自己留下了一些疑问：

-   [\#1] 三方提供的AIDL文件中有的方法不能调用，客服说这个方法不公开。  
    我疑惑是怎么可以在不问客服的情况下知道这个方法是否可以调用呢？或者怎么验证客服说的是否是真的，这个方法真的不公开吗？

-   [\#2]
    代码调不通的时候有点烦躁，就试着瞎改AIDL的某个方法名执行试试，结果发现方法名变更后依然可以调用成功。  
    我疑惑方法名都改了，方法签名肯定跟Server的方法签名不对应了，为什么还能调用成功呢？

-   [\#3]
    居然可以做到对入参内容作修改之后，调用进程那边也能接收到我的修改，就像是入参对象所在的内存跟我修改的对象在同一内存区域一样，在进程间内存隔离的情况下这是怎么做到的呢？

 

通过官方文档学习AIDL
--------------------

官方资料优先，我找到官方的AIDL相关的文档：[Android 接口定义语言
(AIDL)](https://developer.android.com/guide/components/aidl)

通过简单阅读发现AIDL中所描述内容与Service有挺大关系，自己又对Service的细节性API已没有太多印象，遂转去先通读一遍官方的Service相关文档：[服务
(Service)](https://developer.android.com/guide/components/services)

 

通过阅读服务的文档及自己的实践，留下了一些疑问：

-   [\#4]
    我使用onBind方法返回给Activity的IBinder对象在相同进程的情况下，Activity取到的Binder对象可以强转为实际对象(是同一个对象)，而当我给Activity或Service指定一个独立进程的情况下为什么Activity拿到的对象就不是同一个对象了呢？并且也不能强转为我传给Activity的实际类型。

-   [\#5]
    为什么Messenger可以达到[\#4](onenote:#%5btodo%5d201903-5%20Android%20Binder%20机制学习手记&section-id={8FC2D5F2-BF18-49F8-95A0-5BA7AD607994}&page-id={74A8B36E-D5A0-4474-BBF8-D4A102B07BA8}&object-id={37BCA87E-5A52-47B8-9645-2731ED31D77F}&37&base-path=https://d.docs.live.net/a061d30cbaa5dc66/文档/个人文档中)的目的？

 

阅读官方文档：[Android 接口定义语言
(AIDL)](https://developer.android.com/guide/components/aidl)。读完后稍加练习，知道了使用AIDL机制可以跨进程传递AIDL接口对象，并且允许方法调用，Client可以传递参数给Server,
也可以拿到Server的返回值和对参数内容的变更。

-   按照AIDL语法写出一个aidl接口文件

-   构建过程会生成一个对应的Java接口、一个继承了Binder实现了该接口的Stub抽象类

-   Server可以继承Stub并将实例返回给Client

-   Client将Server的aidl接口文件复制到相同包下

-   Client在拿到Server的binder的时候可以通过Stub的asInterface方法获得需要的功能接口的对象，之后就可以直接通过该对象跨进程调用到Server与其通信了

aidl接口支持的参数类型必须是基本类型、String、以及实现了Parcelable的特殊类型，想来肯定是由于进程隔离的原因，将数据序列化处理之后在进程间传输的。

至此回想了一下自己的疑惑，有一些已经被解决：

-   \#1
    由于双方要使用相同的aidl文件生成的java接口进行通信，所以可以直接反编译三方SDK-Server-APK，查看对应的java接口声明的方法即可。

-   \#5
    通过阅读源码发现[Messenger](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-2.3.6_r0.9/core/java/android/os/Messenger.java)的发送Message的[send](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-2.3.6_r0.9/core/java/android/os/Messenger.java#49)方法实际使用的是[IMessenger](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-2.3.6_r0.9/core/java/android/os/IMessenger.aidl)的[send](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-2.3.6_r0.9/core/java/android/os/IMessenger.aidl#24)方法。IMessenger是一个aidl文件，由[Handler](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-2.3.6_r0.9/core/java/android/os/Handler.java)的[getIMessenger](https://android.googlesource.com/platform/frameworks/base/+/refs/tags/android-2.3.6_r0.9/core/java/android/os/Handler.java#557)方法对其作真正实现，Messenger只是将binder转IMessenger和Handler转IMessenger的行为进行了包装。  
    由于Messenger达到目的是使用了aidl跨进程的特性，所以解决了本问题的疑惑

 

探索AIDL的实现
--------------

通过回想\#4，觉得既然我自己直接继承binder实现接口提供给Client是不能使用的，而aidl却可以，说明肯定是aidl在中间帮忙做了什么我没做的操作，遂前往阅读aidl构建过程生成的java接口、Stub的源码。

最后发现最终的通信方式是使用的[Binder](https://developer.android.com/reference/android/os/Binder)类的[transact](https://developer.android.com/reference/android/os/Binder#transact(int,%20android.os.Parcel,%20android.os.Parcel,%20int))方法，以下是该方法的签名：

~~~java
public final boolean transact (int code, Parcel data, Parcel reply, int flags)
~~~

-   其中code为整型标识，多用于Client/Server间约定行为的标识；

-   data为传给Server的参数，Server可以从中取出Client写入的内容；

-   Server端可以向reply参数中写入内容，调用完成后写入的内容将会存在于Client端的reply中；

-   该方法支持两种调用模式：同步、异步。同步会等待Server端完成方法调用，拿到reply，异步则不等待Server方的调用完成，即刻返回；

Client拿到Server端的Binder对象后调用transact方法，Server端Binder对该方法的实现将会被调用，Server将返回的内容写入reply，得以完成一次Client-Server模式的远程通讯。

通过这个方法实现通讯行为会很繁琐，所以Android提供了AIDL来简化实际开发中的繁琐实现。它的实现原理就像是两个不同语言的人无法交流，但是请来了一个懂两方语言的翻译官就可以畅快交流了，而AIDL就是这个懂两方语言的翻译官。

 

![](/img/post/android-binder-study-note/0_aidl.png)

-   提供一个用来约定Client/Server交互接口的规范，写法很像Java接口，以aidl为扩展名

-   解析aidl文件，生成Java交互接口

-   提供针对Client方的代理实现：Stub.asInterface，将Client对交互方法的调用转换为对transact方法的调用

    -   将方法在接口中的声明顺序设为transact的code参数，用以告诉Server要调用哪个方法

    -   创建一个Parcel对象，将方法的入参按顺序写入到Parcel，该Parcel作为transact的data参数，用以告诉Server方法调用的参数

    -   创建一个Parcel对象，该Parcel作为transact的reply参数，用以存储方法调用中Server对参数内容的变更和方法的返回值

    -   transact方法调用完成后，从reply中取出Server对参数内容的变更并写入到原参数对象、取出方法返回值，将返回值返回给交互方法调用方

-   提供针对Server方的代理实现：Stub，将收到的对transact方法的调用转换为调用交互方法的真正实现

    -   取出transact传来的code参数，以该code为序号通过交互方法的声明顺序找到Client要调用的方法

    -   从transact的data参数中按顺序取出方法调用的参数并转调交互方法的真正实现

    -   将交互方法对参数内容的变更按顺序写入到transact传来的reply中，将返回值写入到reply中

    -   完成方法调用

 

AIDL的这种为调用两方提供的代理实现让开发者得以以面向对象的方式进行远程通讯，实际使用起来仿似在调用一个本地对象一样。

 

至此我的一些疑惑也得到解答

-   \#2
    因为对交互接口的方法调用不是使用方法签名来分发的，而是通过方法的声明顺序，所以更改方法的签名并不会对会调用到哪个方法产生影响

-   \#3 AIDL给Server生成的代理实现会将对参数的变更写入reply,
    同时给Client生成的代理实现会将reply中对参数的变更写回到参数，所以就像是他们在同一内存区域一样

-   \#4
    因为AIDL对方法调用的两方提供了代理实现，最终使用的是transact方法调用完成通信

 

我试着不用AIDL实现了Client-Server的通信，没有遇到什么问题，也发现了AIDL接口中不能使用Serializable类型作为方法参数和返回值只是因为AIDL没有对其进行实现（应该是考虑到效率原因）。

 

拨开AIDL，追寻Binder
--------------------

到此时，AIDL在我心目中落下神坛，一切都是Binder的功劳，那……,
Binder是怎么做到给Client-Server两方建立了一个数据传输通道的呢？

可能是因为这个问题属于SDK的实现而不是使用，所以官方没有太多的资料对其进行讲述，所以去试着搜一些三方资料。



__[田维术](http://weishu.me/about/) - [《Binder学习指南》](http://weishu.me/2016/01/12/binder-index-for-newer/)__

这篇文章为指南性文章，讲述

-   一些基本概念铺垫，例如进程隔离、用户/内核空间、系统调用/内核态/用户态，内核模块、驱动

-   Binder机制的通信模型

-   API使用层面上的实现机制

-   之后继续深入学习的指路

读完文章后对API使用层面上的实现机制有所了解，例如Client-Server交互中实际会有两个Binder对象，一个是真实Binder对象，存在于Server端；一个是代理Binder对象，存在于Client端；Client通过代理Binder对象调用transact，binder驱动会查找到Server的真实Binder对象并调用其transact方法  
对基本概念、技术背景、基本实现原理有所了解，但是对具体binder机制是怎么实现的还是会很疑惑



__[universus](https://me.csdn.net/universus) - [《Android Binder设计与实现 - 设计篇》](https://blog.csdn.net/universus/article/details/6211589)__

这真的是一篇很棒的文章，逻辑清晰，内容完善，可以说是Binder相关的内容，除了具体代码什么都讲了

*对Binder的设计细节做一个全面的阐述，首先通过介绍Binder通信模型和Binder通信协议了解Binder的设计需求；然后分别阐述Binder在系统不同部分的表述方式和起的作用；最后还会解释Binder在数据接收端的设计考虑，包括线程池管理，内存映射和等待队列管理等。通过本文对Binder的详细介绍以及与其它IPC通信方式的对比，读者将对Binder的优势和使用Binder作为Android主要IPC方式的原因有深入了解。*

我专门将文章打印了出来反复研读，随着阅读次数的增加，自己对binder的各个角度的疑惑都被慢慢消除

 

总结
----

我很喜欢[universus](https://me.csdn.net/universus)对binder机制的比喻：

*面向对象思想的引入将进程间通信转化为通过对某个Binder对象的引用调用该对象的方法，而其独特之处在于Binder对象是一个可以跨进程引用的对象，它的实体位于一个进程中，而它的引用却遍布于系统的各个进程之中。最诱人的是，这个引用和java里引用一样既可以是强类型，也可以是弱类型，而且可以从一个进程传给其它进程，让大家都能访问同一Server，就象将一个对象或引用赋值给另一个引用一样。Binder模糊了进程边界，淡化了进程间通信过程，整个系统仿佛运行于同一个面向对象的程序之中。形形色色的Binder对象以及星罗棋布的引用仿佛粘接各个应用程序的胶水，这也是Binder在英文里的原意。*

 

本想继续深入将binder各部分的实现源码捋一捋读个遍，但由于对Linux的实现原理及C语言不很熟悉，同时还有一些其他的学习计划在队列中，所以暂时搁浅这个想法。

 

### 学习方法总结

本篇文章为记录型文章，记录了Binder机制的学习历程。但是是后期补写的，期间也重新捋了捋知识，一些由于犯懒没去验证的想法也去翻了翻源码得以验证。

可以感受到，写下来，确实是可以强制自己把要写的东西确保完全正确，在这个过程中也能加深自己对知识的印象和理解。

但是，我很明显的感觉到，真的耗费了很多时间，学习效率太低了。针对Binder机制的学习，觉得在三方资料良好的情况下，对学到的内容进行详实的记录是没有太大的必要的。

可以像是学习一个官方特性的时候，看看特性应用的环境、功能、实现过程、实现原理，把有关资料全部过一遍，然后相关练习做一遍就可以直接使用了，我可能会忘掉具体怎么实现的，但是我不会忘记他的环境、功能甚至原理，我只要在当我需要但忘记他的时候再次前往资料，快速或针对查阅即可。当然这是针对有完备可信的资料随时以供查阅的情况下，对于一些资料不全、难以理解的技术还是要有相应的笔记、总结等辅助学习。

另外由于本次学习期间有使用打印文章阅读的方法，并且遇到什么问题就直接很简单的划线、标注了，觉得这个过程对资料完备的知识学习很愉悦，一方面文章中讲述的有自己直接吸收的思想，一方面自己又可以基于此直接作相关补充。但是纸质内容的检索和存放都是很不方便的，之后要试着将这种学习方式搬到电脑上，纳入自己日常新知的学习过程中。