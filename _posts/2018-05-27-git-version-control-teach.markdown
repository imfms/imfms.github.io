---
layout: post
title:  "版本控制-新人培训记录"
subtitle: "我们的最终目的是为了让我们在有限的精力和时间内去做更重要的事情"
date:   2018-05-27 17:34:30 +0800
categories: version-control
tags: 
- 版本控制
- git
---



此版本内容为通过《20180510-2新人培训大纲-版本控制]》对新人进行培训后，对培训过程中产生内容的记录，同时进行了一些更为合理的修改。目的为从大纲整理出一份完整的文档，作为新人接受培训前/后的参考文档。

名词声明
========

-   \${变量名}  
    表达一个变量，实际值由所处的上下文环境和变量名动态决定





情景模式
========

开发新项目
----------

收到新项目开发通知，设计完成后，在我们的不懈努力下，项目得以如期完成



优化项目性能
------------

接到大量反馈说项目运行缓慢，影响到了正常使用，所以新版本需要优化。

我们设计了一个新的方案来替换项目中的性能瓶颈代码，可是在更改过后发现新方案行不通，考虑重新设计然后重新开始。于是我们先凭记忆将修改后的代码一步步推回到没有修改时的状态，再去着手新的设计。



项目性能优化完成
----------------

通过我们优化项目性能过程中发现，我们在实验新想法发现行不通的时候，将代码推回未修改的状态时花费了很多时间，并且修改内容量很大的情况下凭借记忆并不一定能够修改回原来的状态，可能会给项目带来不必要的隐患。

所以我们需要一个可以随时将项目恢复到指定状态的方案。



保存/恢复项目状态方案设计：时光倒流
-----------------------------------

为方便交流我们作以下名词定义

-   此方案叫做'时光倒流'
-   项目的根目录称为'工作区'
-   工作区中创建一个'.backup'的文件夹，这个文件夹叫'备份库'
-   备份库中可以新建N个叫做'存档点'的文件夹，存档点的命名规则为按照被创建的先后顺序从0开始进行命名，第一个为0,
    第二个则为1,依次类推
-   存档点中需要包含一个名为'.metadata'的文本文件，此文件被称之为'存档信息'，存档信息中需要包含此存档点的创建时间
-   如果要网络备份的话直接将备份库上传即可


![时光倒流结构图](/img/post/version-control-teach/时光倒流.png)

每次需要存档的时候只需要在备份库新建一个存档点，然后将工作区的内容复制到存档点，并编写相应的存档信息

待到需要恢复到某一个存档点状态的时候，只需将存档点的内容复制到工作区即可达到恢复的目的

'时光倒流'方案同时解决了我们回退项目难度大与代码无备份的问题。



项目A功能变更为B功能
--------------------

收到通知要求项目中的A功能要变更为B功能，我们在设计好变更方案后开始着手去做。

变更过程中发现A功能中有一段奇怪的代码，无法揣测其目的，我们也只是微微记得当时发现了一个很隐晦的问题才写下的。但是实现B功能必须修改此代码，我们难以验证修改或删除这段代码后是否会产生无法预料的影响，所以不敢轻举妄动。

最终我们还是实现了B功能，但是召集了项目中所有人员，花很大精力对新功能进行测试，为此项目人员怨声载道。



时光倒流之事出有因 
------------------

通过功能变更过程中我们发现历史代码的变更如果没有对其做出描述，会给后期维护带来很大的麻烦，于是我们增强了时光倒流方案：'时光倒流之事出有因' 

我们要求开发者在存档的时候为存档信息中添加修改内容的语义化描述，该描述被称之为'存档注释'

![时光倒流之事出有因结构图](/img/post/version-control-teach/时光倒流之事出有因.png)



B功能升级
---------

收到通知，项目B功能需要升级为B2功能。

制定方案，着手去升级。

这次又发现了一段“奇怪”的代码，我们坦然的查询了这段代码在存档点中的变更记录，然后查看存档注释，想着我们这次肯定不会出现上次的尴尬状况了。

打开存档注释，我们懵了，注释内容为：'0527存档'

最终我们还是实现了B2功能，并再次召集了项目中所有人员，再次花很大精力对新功能进行测试，为此项目人员再次怨声载道。



时光倒流之事出有因之我们没有关系就不要在一起了
----------------------------------------------

B2功能发布后，我们分析了发布过程中的问题。

我们的存档注释出现了“0527存档”这种情况。

很明显，这是我们在0527那天下班的时候执行了存档操作。

我们为什么要在下班的时候执行存档操作？并且存档注释居然没有所写内容的任何相关描述？

1.  传统思想，我们当天工作完成之后一般都要交接一下工作，所以执行一下存档操作，同时也可以为自己下班带来一些仪式感，暗示自己今天的工作完成了，可以好好休息一下了

2.  编程工作不同于流水线/营业额等可以简单量化的工作，当下班的时候我们已经记不得上午做了什么东西了，如果真的要详细列出，恐怕存档注释都要写半个小时了，所以0527存档这个名字是个好主意

很明显，我们不能责怪我们，我们生来想要仪式感，我们生来想要偷懒。
我们，生来需要约束。

我们对以后的开发工作中对使用'时光倒流之事出有因'方案作以下约束：

-   如果只是用来备份代码，那我们的'时光倒流'方案就可以做到，就没必要设计时光倒流之事出有因了

-   我们的'时光倒流之事出有因'方案不仅仅是用来备份代码的，更重要的是记录变更原因

-   每个存档操作必须指定存档注释，注释内容要描述出距离上一次存档以来所作变更的结果或原因  
    非必要情况不要描述实现细节，那应该是代码注释做的事情

-   如果你的存档做了很多修改以至于难以用注释简单描述，这说明你本应该将他们拆分成多个存档

-   写新代码前想一想我要写的新代码与当前未存档的代码有没有关系？如果没有，请先对当前代码执行存档

-   当前代码如果存在低级错误，请解决完后再执行存档  
    例如语法错误等简单错误

-   下班前代码没写完，还不能组成一个存档点？没事，为了后期查阅存档注释的友好，先不存档也罢，等到写完再存档也不晚


这么做真的值得吗？恩，值得，我们的民族自豪感促使我们异口同声的说。 



新功能C和新功能D
----------------

收到开发新功能C和新功能D的通知，且要在下个版本推出这两个功能

项目开发进度平稳，相安无事。可是在整体完成3/4的时候，上级通知C功能需紧急发布，D功能暂未开发完成，所以等到下一个版本单独发布。

可是当前代码中除了C功能还有刚完成了一半的D功能，我们只得匆匆忙忙把项目做了个备份，然后将D功能的代码一一删除，然后先发布C功能。



新功能C发布后
-------------

在预先发布C功能的时候我们都手忙脚乱的，这不是一个让人愉快的过程。

既然C功能与D功能是相互独立的，那我们能不能设计一个操作方案能让它他们在开发过程中不影响彼此呢？



新方案：时光倒流之事出有因及平行时空
------------------------------------

-   本方案继承自时光倒流之事出有因

-   本方案叫做'时光倒流之事出有因及平行时空'

-   备份库中除了可以存在存档点，还可以存在'子备份库'

-   ".backup"备份库被改称为主备份库

-   子备份库基于父备份库的某个存档点

-   子备份库拥有备份库的所有功能  
    当然，子备份库也可以包含子备份库

-   子备份库的命名方式为'.\${基于的存档点}.\${子备份库名}'  
    例如基于存档点10的要写B功能的子备份库则可以命名为：.10.feature-b



![时光倒流之事出有因及平行时空结构图](/img/post/version-control-teach/时光倒流之事出有因及平行时空.png)

用这个方案，假设当前最新的存档点为10，我们在接到C和D功能的时候，我们只要新建两个子备份库
.10.feature-c和.10.feature-d，有关C的代码写到.10.feature-c，有关D的代码写到.10.feature-d，然后遇到要提前发布C的情况，就在主备份库新建一个存档点，把.10.feature-c包含的所有存档点的变更汇总到主备份库的新存档点，这样就能独立完成了C功能的提前发布而不需要回退任何关于D功能的代码。



从平行时空回来，一切都变了
--------------------------

在新方案制定后我们用新方案模拟了上次CD两个功能中发布单独一个C功能的场景，没有出现任何问题。

我们自信心满满的唱着我们始终在进步这次又向前迈了一大步。 

转眼间D功能完成了，我们按照新方案发布C功能的流程一样去发布D功能，一切正常，我们感觉到自己实现了人生价值。

十分钟后，线上报警，经检查后确认新版本中C功能存在重大问题，遂赶紧将线上回退到上一个正常版本。

我们懵了，我们上的是D功能啊，C怎么就出问题了 - -

最终我们在主存储库的最后一个存档点(把.10.feature-d的变更汇总过来的存档点)重现了问题

经排查后找到原因：

.10.feature-c备份库与.10.feature-d备份库都变更了公共库代码util.java

-   .10.feature-c修改了go方法

-   .10.feature-d修改了back方法

汇总时

-   当.10.feature-c汇总到主备份库时主备份库接受了util.java，C功能测试没问题。

-   当.10.feature-d汇总到主备份库时主备份库接受了util.java，D功能测试没问题。可是由于全部接受了util.java，故.10.feature-c的util.java的变更丢失，导致了线上的重大问题



结论：平行时空方案在不同备份库汇总时存在漏洞。 

找到问题的原因了，赶紧先匆匆忙忙修改后重新部署。



修补平行时空交叉的漏洞
----------------------

经过上一次的事故，我们很认真的对平行时空的漏洞做了很严谨的分析，并制定了良好的解决方案。 

在两方存储库的变更汇总过程中会存在以下几种情况：

-   文件无变更

-   文件单方面变更  
    某个文件只在其中一个备份库被修改

-   文件双方面变更  
    某个文件被两个备份库同时修改



本方案对各情况的处理方法

| 变更情况    | 处理方式      | 原因                                       |
| ------- | --------- | ---------------------------------------- |
| 文件无变更   | 不处理       | 双方没有提出任何新观点，故也没有异议，所以不处理                 |
| 文件单方面变更 | 听从变更方     | 一方提出新观点，另一方没有异议，所以听从变更方                  |
| 文件双方面变更 | 交由第三方人员处理 | 一方提出新观点，另一方也提出新观点，双方各执己见，无法做出最终评判，故交给第三方人员处理 |

其中，执行汇总操作的“我们”为表中的第三方人员，在文件双方均存在变更的情况下，方案不能替我们做出决定，因为方案做出的决定并不能如我们所愿(例如D功能发布导致C躺枪事件)，此时我们必须主动介入把不同变更以合情合理的方式汇总到一起。



我们以“合情合理”方式处理双方变更的常见方式

| 情况                  | 处理方式                        |
| ------------------- | --------------------------- |
| 一方的观点更好             | 听从观点更好的一方                   |
| 两方提出的观点各有各自的好且不相互冲突 | 两方都听从 以当前文件类型的内容组织方式将两方观点容纳 |
| 两方提出的观点各有各自的好但相互冲突  | 新提出一个两方都同意的观点               |

这样，就不会再出现D功能发布C功能躺枪的情况了。 

我们佩服自己的缜密。



增强平行时空交叉的能力
----------------------

通过项目近期的更新迭代过程中我们发现实际开发情况下会出现很多文件双方面变更。

所以，为了减少我们的工作量，我们总结出了一些“可能”不需要“我们”参与的汇总情形

| 情况             | 是否需要“我们”参与 | 为什么                                      |
| -------------- | ---------- | ---------------------------------------- |
| 双方变更不在同一“私有领地” | 不需要        | 相当于“文件单方面变更”的处理方式，只不过变更区域从“文件”级别变成了“私有领地”级别 |
| 双方变更在同一“私有领地”  | 需要         | 相当于文件“双方面变更”的处理方式，只不过双方面变更区域从“文件级别”变成了“私有领地”级别 |

其中不同的文件类型的内容组织方式不同，故“私有领地”的界定方法也不同，以下举出一些例子

| **文件类型** | 纯文本文件     |            |
| -------- | --------- | ---------- |
| 私有领地的界定  | 单个字符      |            |
| **举例**   | **不冲突情况** | **冲突情况**   |
| 源        | 0000      | 0000       |
| 一方修改后    | aa00      | aaaa       |
| 另一方修改后   | 00bb      | bbbb       |
| 汇总后      | aabb      | ${需人工参与解决} |


| **文件类型** | java源文件                                  |                                        |
| -------- | ---------------------------------------- | -------------------------------------- |
| 私有领地的界定  | java方法提内代码块<br />空行空格等无逻辑符不属于变更<br />(程序语言的私有领地界定方式，在此为了演示只举一个简单例子，仅供参考，并不严谨) |                                        |
| **举例**   | **不冲突情况**                                | **冲突情况**                               |
| 源        | void foo(){}; void foo2(){};             | void foo(){}; void foo2();             |
| 一方修改后    | void foo(){return;}; void foo2(){};      | void foo(){// return;}; void foo2(){}; |
| 另一方修改后   | void foo(){ }; void foo2(){return;};     | void foo(){return;};void foo2(){};     |
| 汇总后      | void foo(){return;}; void foo2(){return;}; | \${需人工参与解决}                            |


| **文件类型** | 位图图像类文件                                  |                                          |
| -------- | ---------------------------------------- | ---------------------------------------- |
| 私有领地的界定  | 单个像素<br />(仅供参考，实际情况可能更复杂)               |                                          |
| **举例**   | **不冲突情况**                                | **冲突情况**                                 |
| 源        | ![位图冲突对比_源](/img/post/version-control-teach/位图冲突对比_源.png) | ![位图冲突对比_源](/img/post/version-control-teach/位图冲突对比_源.png) |
| 一方修改后    | ![位图冲突对比_1_1](/img/post/version-control-teach/位图冲突对比_1_1.png) | ![位图冲突对比_2_1](/img/post/version-control-teach/位图冲突对比_2_1.png) |
| 另一方修改后   | ![位图冲突对比_1_2](/img/post/version-control-teach/位图冲突对比_1_2.png) | ![位图冲突对比_2_2](/img/post/version-control-teach/位图冲突对比_2_2.png) |
| 汇总后      | ![位图冲突对比_1_3](/img/post/version-control-teach/位图冲突对比_1_3.png) | \${需人工参与解决}                              |

所以只要根据不同的文件类型实现一个其特有的私有领域的对比工具即可

该工具提供两个行为

-   判断汇总行为是否需要人工参与

-   汇总两个变更，返回一个汇总后文件


我们又一次深深感到自己人生价值的体现,就仿佛我们改变了这世界。



以上，就是版本控制
----------------

[版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统](https://git-scm.com/book/zh/v1/%E8%B5%B7%E6%AD%A5-%E5%85%B3%E4%BA%8E%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)

其中，我们方案中的“增强平行时空交叉的能力”属于我们的构思，大多版本控制工具只提供了文本类型文件的以'行'为单位的私有领域的汇总实现。



三方成熟版本控制工具学习：Git
=============================

**注意：我们的实现方案目的只为辅助理解版本控制概念，与Git的实现方式无任何关系。**

Git是一款开源的强大灵活的分布式版本控制系统，是目前使用最广泛的同类型工具。

对于我们方案中的'增强平行时空交叉的能力'，Git只提供了文本文件类型的以'行'为单位的私有领域汇总实现。这意味着双方面变更了同一个文件且这个文件是文本类型的情况下，如果变更位置不在同一行，Git可以以'增删改行'的方式帮忙汇总，否则必须由人工参与解决。



名词参照

*由于实现方式不同，名词的参照可能与实际在git的概念中不完全符合，但大意基本相通*

| 我们方案                  | Git方案          |
| --------------------- | -------------- |
| 工作区                   | 工作区            |
| 方案所需数据及资源存储位置 .backup | .git           |
| 存储库                   | 分支             |
| 存档                    | commit         |
| 存档时间                  | commit 时间      |
| 存档注释                  | commit message |
| 主存储库                  | 主分支            |
| 子存储库                  | 子分支            |
| 创建一个子存储库              | 创建一个子分支        |
| 上传到服务器                | push           |
| 不同存储库汇总               | 合并分支           |
| 双方面变更需人工参与            | 处理冲突           |

Git介绍
-------

[Git - 官方中文用户手册](https://git-scm.com/book/zh/v1)



Git分支工作流了解更多
---------------------

-   [Git分支管理策略](http://www.ruanyifeng.com/blog/2012/07/git.html)
-   [Git工作流程](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)
-   [Understanding the Github Flow](https://guides.github.com/introduction/flow/)





基于Git的知名远程仓库
---------------------

-   [Github](https://github.com/)面向开源及私有项目的托管服务，同时也被称为全世界最大的“程序员交流社区”  
    其中托管开源项目免费，私有项目需付费
-   [BitBucket](https://bitbucket.org/)面向开源及私有项目的免费托管服务
-   [GitLab](https://gitlab.com)面向开源及私有项目的免费托管服务  
    因为网站本身开源，所以企业内部架设git远程仓库一般首选GitLab
-   [Gitee(码云)](https://gitee.com/)国内面向开源及私有项目的免费托管服务
-   [Coding](https://coding.net/)国内面向开源及私有项目的免费托管服务





### 远程仓库的拓展功能

-   Star

    *你可以为仓库添加星标以表示你喜欢或感兴趣*

    -   [About stars](https://help.github.com/articles/about-stars/)

-   Follow

    *你可以关注远程仓库上的的其它用户，收到有关他活动的通知*

-   [Following people](https://help.github.com/articles/following-people/)

-   Issue

    *Issue指的是一项待完成的工作，通常与系统的改进相关，中文可以译为"问题"或"事务"。*

    -   [如何用Issue管理软件项目？](http://www.ruanyifeng.com/blog/2017/08/issue.html)
    -   [About issues](https://help.github.com/articles/about-issues/)

-   Fork

    *Fork可以创造一个仓库的副本到你的账户。该仓库允许您在不影响原始项目的情况下自由的尝试更改。同时也是向其它仓库贡献内容的基础步骤。 *

    -   [Fork a repo](https://help.github.com/articles/fork-a-repo/)

-   PullRequest

    *通过PullRequest可以告诉仓库所有者关于你在他仓库的变更。一旦PullRequest被发起，你们可以讨论发生的变更，仓库所有者可以将你的变更合并到他的仓库。在变更合并到仓库之前你还可以继续添加后续提交。*

    -   [About pull requests](https://help.github.com/articles/about-pull-requests/)

-   Wiki

    *为你的项目添加wiki*

    -   [About GitHub Wikis](https://help.github.com/articles/about-github-wikis/)

-   Pages

    *Pages是一个静态站点托管服务，旨在直接从仓库托管你的个人，组织或项目页面*

    -   [What is Github Pages?](https://help.github.com/articles/what-is-github-pages/)

-   Webhook

    *Webhooks提供了一种方法，可以在仓库或组织中发生特定操作时向指定Web服务器添加通知*

    -   [About Webhooks](https://help.github.com/articles/about-webhooks/)

    -   通过Webhooks自动部署项目实例

![通过Webhooks自动部署项目实例](/img/post/version-control-teach/通过Webhook自动部署项目实例.png)



Git拓展内容
-----------

-   [Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
-   [Git 使用规范流程](http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)





推荐操作方式
------------

-   Git官方手册过一遍，体验/理解一遍基本操作命令
-   Git命令很多很复杂，以至于可以写一本书来讲述
-   请不要背命令，而是理解命令的意义，在你需要它的时候即时查阅
-   如果我们现有的版本控制能力可以支持目前的开发，且在各种情况不会遇到尴尬的处境，那就没有太大的必要去主动学习新的版本控制能力(个人兴趣除外)
-   主流IDE(甚至是高级编辑器)大多对版本控制提供了GUI支持且有着非常nice的交互方式，请首选此类工具，次选命令行操作方式
-   如果一个程序员不熟悉版本控制，请不要投以鄙夷的目光，他可能只是不知道或选择暂时搁浅这个领域





展望
====

当然是希望我们设计的方案中的'增强平行时空交叉的能力'能够出世与普及咯～

最后
====

我们学习与应用版本控制的最终目的是为了让我们在有限的精力和时间内去做更重要的事情。

参考链接
========

-   [Git Document](https://git-scm.com/doc)

-   [GitHub Help](https://help.github.com/)

-   [ydiff-结构化的程序比较](http://www.yinwang.org/blog-cn/2013/04/21/ydiff-%E7%BB%93%E6%9E%84%E5%8C%96%E7%9A%84%E7%A8%8B%E5%BA%8F%E6%AF%94%E8%BE%83/)

-   [如何在一款考验人性的游戏中玩出民族自豪感](https://www.douban.com/note/667992921/?from=tag)

