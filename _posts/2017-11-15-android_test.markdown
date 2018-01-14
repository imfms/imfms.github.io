## Android测试学习手记

每次发版前，内心总会控制不住的开始焦虑，虽然测试代码的验证通过不能证明代码没有问题，但至少它能证明这庞大的历史代码还是相对安全的。

眼前代码的修改会不会导致其他问题的出现，我不知道，我可能需要细细查阅，细细摩挲，才能怯懦的告诉你：“应该不会吧”。

欲来欲能发觉人脑能同时顾及的东西太少太少，而真实情况下要顾及的东西又太多太多。

我只想关心当下我的业务逻辑与我的每一行代码实现，请让我忘记历史带给我的包袱与压力，让我享受当下。

TDD，我来了。

### 学习路程

#### 1. JUnit4学习, 相关文档阅读

在保证学习的易懂又不失系统情况下，选择了一些中文博文文章配合官方文档进行学习

- [在Eclipse中使用JUnit4进行单元测试（初级篇）](http://blog.csdn.net/andycpp/article/details/1327147)
- [在Eclipse中使用JUnit4进行单元测试（中级篇）](http://blog.csdn.net/andycpp/article/details/1327346)
- [在Eclipse中使用JUnit4进行单元测试（高级篇）](http://blog.csdn.net/andycpp/article/details/1329218)
- [JUnit4 Offical Document](http://junit.org/junit4/)
- [探索 JUnit 4.4 新特性](https://www.ibm.com/developerworks/cn/java/j-lo-junit44/)
  配合Junit4 Offical Document辅助学习

在测试框架出现之前，开发者们撰写测试用例会很痛苦，有很多问题需要面对，测试用例的，执行时机，测试规范，测试覆盖量无法衡量，历史测试维护，测试用例复用... 。想说爱你不容易

JUnit4是一个java单元测试框架，框架提供易懂易用且不失灵活性和扩展性的辅助测试功能，封装了大多在测试代码中所需要的功能：结果断言测试，异常测试，方法执行超时测试，测试前/后 预备/收尾工作，批量测试，分类测试，假设测试，理论测试... ... 并且有各大java ide及插件的良好支持(eclipse, IntelliJ).

#### 2. Junit练习, 给自己的小类库撰写测试

还是做做题吧，不然连自己什么都不会这件事都不知道

[java-library - multivalue, check library add unit test](https://github.com/imfms/java-library/commit/3563b799e9e3dc262f6fb90f34e3adda33e0acb3)

#### 3. Android测试学习, 相关文档阅读 

宏观了解在Android开发中通常使用测试手段

- [Testing Apps on Android](https://developer.android.google.cn/training/testing/index.html)
- [Test Apps on Anroid Studio](https://developer.android.google.cn/studio/test/index.html)

#### 4. 调用任何方法都会throw exception的android.jar 

> By default, the [Android Plug-in for Gradle](https://developer.android.com/tools/building/plugin-for-gradle.html) executes your local unit tests against a modified version of the `android.jar` library, which does not contain any actual code. Instead, method calls to Android classes from your unit test throw an exception. This is to make sure you test only your code and do not depend on any particular behavior of the Android platform (that you have not explicitly mocked).
>
> 默认情况下，用于[Android Plug-in for Gradle](https://developer.android.com/tools/building/plugin-for-gradle.html) 执行本地单元测试，会提供一个修改版本的`android.jar`，它不包含任何实际的代码。相反，从单元测试中调用Android SDK的所有方法皆会抛出异常。这是为了确保您只测试您的逻辑代码，并且不依赖于Android平台的任何特定行为(在没有明确地进行Mock的情况下)。

- [Mock Android dependencies](https://developer.android.com/training/testing/unit-testing/local-unit-tests.html#mocking-dependencies)


如果希望android.jar中被调用的api不抛出异常，那么还有一个选择就是所有api的返回值为默认值，需要在module级别的build.gradle文件中配置如下代码

~~~groovy
android {
  ...
  testOptions {
    unitTests.returnDefaultValues = true
  }
}
~~~

当然，此种情况下开发者需要考虑撰写的单元测试代码会不会因为所有的api返回都为默认值而导致的有些可能被忽略的错误

#### 5. 幻想一切, 不受制于环境, Mockito

Mockito是一款Java平台模拟类行为的框架，以Java动态代理机制为基础，有着简单易用的api，多服务于Java单元测试作业。

项目开发过程中，被测试的代码中会存在一些对外部环境行为依赖的情况，同时由于种种原因不能将其真实引入测试环境

- 外部环境不稳定，属于不可控因素，不能保证外部环境时时准备待命
- 想要对 '代码对外部环境的某个行为做出的响应' 正确与否进行测试，可是外部环境想要达到该行为的成本很高
  - 时间成本, 外部环境达到该行为要耗费很多时间，降低测试效率
  - 精力成本, 外部环境达到该行为要执行各种五花八门的步骤
  - 金钱成本, 外部环境达到该行为每次就是要花一块钱，你就说测不测
- ... ...

**目的驱动**，当我想要测试这段代码是否对外部做出了正确响应的时候，我的目的是测试这段代码是否对外部做出了正确响应，所以所有与直接达到该目的无关内容，都不应该存在。代码不同于人生，不需要此生多娇通灵万物处处感悟。

学习Mockito的过程中像学习其他内容一样，碎片时间里搜索各种入门博文，也读了很多，觉得关于Mockito的学习已经可以了，大部分常用操作已经没问题了，大不了遇到解决不了的现查就好。忙忙碌碌闲闲适适工作后想起之前的一句不知是别人说的还是自己说的话：“你所了解的知识会影响你解决问题的思路与方法”。决定还是在这休息日略读一遍Mockito官方文档。

注：本来对Mockito的文档是拒绝的,因为是javadoc格式, 但后来认真看了下其实是把文档写到了javadoc中，官方也解释了原因：“让开发人员随时记录他们编写的代码而保持文档与代码的同步更新”。(后期写文档的时候各种苦逼可不是吗 :) 

- [Mockito framework](http://site.mockito.org/)
  Mockito 项目官网，在介绍中有指向最新版本javadoc的链接

在Android测试中Mockito配合调用任何方法都会throw exception的android.jar对Android这个“外部环境”进行模拟再合适不过咯 :)

6. ​

N. 阅读及学习http://chriszou.com/2016/06/07/android-unit-testing-everything-you-need-to-know.html测试系列文章








