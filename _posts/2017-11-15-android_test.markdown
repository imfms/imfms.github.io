## 安卓测试学习手记

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

#### 4. 调用任何方法都会crash的android.jar 

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










