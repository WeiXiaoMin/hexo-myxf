---
title: 【Flutter】问题记录
author: 暮野夕风
top: true
cover: false
categories:
  - Flutter
tags:
  - Flutter
  - Android
date: 2019-08-01 02:01:55
img:
coverImg:
summary:
---


## gradle 问题

### Exception in thread "main" java.lang.NullPointerException（window7系统）

> 参考：[简书-flutter运行报错exited abnormally](https://www.jianshu.com/p/c966e58dfb79)

在 AndroidStudio（3.4.2） 中创建 Flutter 项目后立即运行，报了如下异常：
```
Finished with error: ProcessException: Process "C:\_code\flutter\flutter_app\flutter_app\android\gradlew.bat" exited abnormally:
Exception in thread "main" java.lang.NullPointerException
	at org.gradle.wrapper.BootstrapMainStarter.findLauncherJar(BootstrapMainStarter.java:34)
	at org.gradle.wrapper.BootstrapMainStarter.start(BootstrapMainStarter.java:25)
	at org.gradle.wrapper.WrapperExecutor.execute(WrapperExecutor.java:129)
	at org.gradle.wrapper.GradleWrapperMain.main(GradleWrapperMain.java:61)
  Command: C:\_code\flutter\flutter_app\flutter_app\android\gradlew.bat -vFinished with error: ProcessException: Process "C:\_code\flutter\flutter_app\flutter_app\android\gradlew.bat" exited abnormally:
Exception in thread "main" java.lang.NullPointerException
	at org.gradle.wrapper.BootstrapMainStarter.findLauncherJar(BootstrapMainStarter.java:34)
	at org.gradle.wrapper.BootstrapMainStarter.start(BootstrapMainStarter.java:25)
	at org.gradle.wrapper.WrapperExecutor.execute(WrapperExecutor.java:129)
	at org.gradle.wrapper.GradleWrapperMain.main(GradleWrapperMain.java:61)
  Command: C:\_code\flutter\flutter_app\flutter_app\android\gradlew.bat -v
```

打开“android/gradle/wrapper/gradle-wrapper.properties”文件，将 gradle 的版本改为本地的最新版本，如下：
```
distributionUrl=https\://services.gradle.org/distributions/gradle-5.1.1-all.zip
```

重新 Run 'main.dart'，便正常了。尽管原来的“4.10.2-all”版本在本地也是有的。

