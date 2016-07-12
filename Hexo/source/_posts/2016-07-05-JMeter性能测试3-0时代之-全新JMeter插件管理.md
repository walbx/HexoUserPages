---
title: JMeter性能测试3.0时代之-全新JMeter插件管理
date: 2016-07-05 09:42:56
categories: 我用
tags: 性能测试
---
![](http://upload-images.jianshu.io/upload_images/1936843-ea3b7bb202cd0be5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 今年五月，老牌开源性能测试工具[Apache JMeter](http://jmeter.apache.org/)迎来了自2011年11月成为Apache顶级项目以来的首次大版本更新--从2.13更新到3.0。
这对于广大JMeter用户来说无疑是一个好消息，它让我们看到了这个项目的活力。也是因此，打算写一些自己感受到的JMeter近来的变化。

<!-- more -->

## JMeter
[JMeter](http://jmeter.apache.org/)，老牌，开源，轻量，Apache基金会的顶级项目，光是这些关键字就足以让大量用户将其纳入自己的性能测试工具箱。而从实际看来，其在国内的用户数量，足以和著名的LoadRunner分庭抗礼，甚至在如今的互联网浪潮下，其覆盖范围可能已经超越了LR，甚至在其他领域，如接口测试，也能看到JMeter的身影。对于这样的发展趋势，我也非常乐意看到，因为我最初做性能测试时，选择了使用JMeter作为主力工具。

然而最近两年，JMeter并没有太多值得关注的更新。虽然现在的我已经不再是性能测试的萌新，不会被一个工具所制约，但是作为帮助我入门的工具，还是希望能够看到一个更活跃的JMeter，更活跃的JMeter生态。

今年夏天，首先得到的好消息是JMeter迎来了它的3.0版本，其中一个重要的更新就是HTML页面形式的性能测试报告，这一方面它终于是赶上了[Gatling](http://gatling.io/#/)(*关注Gatling主要因为两方面：一是其甩JMeter几条街的性能，二就是其出色的report*)。

然而今天这篇文章并不是讲JMeter 3.0的新特性(*废话半天竟然又不讲这，我自己都醉了。主要是由于时间不够，3.0的新特性，计划放到下一篇文章*)，今天要提的是第二个消息，不是来自于JMeter自身，而是**JMeter Plugins**。

## JMeter Plugins
一直以来，[JMeter Plugins](http://www.jmeter-plugins.org/)为我们提供了很多高价值的JMeter插件，比如:
- 用于服务器性能监视的[PerfMon Metrics Collector](http://jmeter-plugins.org/wiki/PerfMon)
- 用于建立压力变化模型的[Stepping Thread Group](http://jmeter-plugins.org/wiki/SteppingThreadGroup)
- 用于Json解析的[JSON Path Extractor](http://jmeter-plugins.org/wiki/JSONPathExtractor)
- 用于展示响应时间曲线的[Response Times Over Time](http://jmeter-plugins.org/wiki/ResponseTimesOverTime)
- 用于展示TPS曲线的[Transactions per Second](http://jmeter-plugins.org/wiki/TransactionsPerSecond)
非常感谢这些插件的贡献者很大程度上丰富了JMeter的生态，并直接造福了广大的JMeter使用者。

在以前，这些插件的安装还是一个纯手工的方式：所有插件分为四个集合包，首先需要找到包含目标功能的集合包-下载该依赖包-拷贝的合适的路径-重启JMeter。这样的过程对于刚接触JMeter的新人来说，可能稍显繁琐。

## Plugins Manager
值得高兴的是，最近，**jmeter-plugins.org**推出了全新的[Plugins Manager](http://www.jmeter-plugins.org/wiki/PluginsManager/)，对于其提供的插件进行了集中的管理，我们只需要安装这个管理插件，即可以在JMeter的界面上搜索并安装指定的插件。简要步骤如下：
1. 下载管理插件的[JAR文件](http://jmeter-plugins.org/get/)
2. 将下载的文件拷贝的你的JMeter根目录下的`lib/ext`目录
3. 启动JMeter，点击`菜单栏`-`Options`-`Plugins Manager`,如`图1`：
![图 1](http://upload-images.jianshu.io/upload_images/1936843-4a2e153a94ed3a24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4. 在如`图2`的管理页面进行插件管理，共有三个标签页:
![图 2](http://upload-images.jianshu.io/upload_images/1936843-13d80ced4b63dd3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  - Installed Plugins：顾名思义，是用于查看已安装的插件，并可通过`取消勾选`-`应用操作`来卸载插件
  - Available Plugins:用于查看和安装可用的插件
  - Upgrades:用于升级插件

另外，Plugins Manager还提供了命令行安装的支持，具体参见[官方wiki](http://jmeter-plugins.org/wiki/PluginsManager/)，以便让在Linux上或者以NO-GUI方式在windows运行的使用者也能快捷的进行JMeter插件管理。

最后，快快去体验一下吧 : )