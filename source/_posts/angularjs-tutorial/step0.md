---
title: Angularjs Tutorial
description: 通过这个简单的教程，能够使你更加清晰地了解Angularjs网页应用的构成。跟随这个教程，你将会完成一个能展示手机列表的网页应用，这个应用包含关键字搜索，查看详情页等功能。
date: 2015.08.01
tags:
- 博客
- angularjs
- javascript
category: 日志
---

_注：本系列文章翻译自Angularjs官方范例教程，本人英语三级未过，翻译能力有限，附上原版网址：http://docs.angularjs.cn_

## PhoneCat Tutorial App

通过这个简单的教程，能够使你更加清晰地了解Angularjs网页应用的构成。跟随这个教程，你将会完成一个能展示手机列表的网页应用，这个应用包含关键字搜索，查看详情页等功能。
![](/img/angularjs_tutorial/catalog_screen.png)
通过这个简单的教程，你将会发现Angular可以在完全不需要任何本地扩展和插件的情况下，让网页应用变得如此美妙：
 * 通过使用客户端的 data binding 来绑定动态视图的数据，这样数据就能够实时地将用户的操作反应出来。
 * Angular可以在不借助DOM操作的情况下同步你视图的变化。
 * 使用 [Karma](http://karma-runner.github.io/0.13/index.html) 和 [Protractor](http://www.protractortest.org) 使你的测试变得更加简单，高效。
 * Angular使用依赖注入和大量的服务来创建通用的模块，这将使构建代码更加容易。

当你完成了这个教程后，你将会get如下技能：
 * 制作一个适用于所有流行浏览器的动态网页应用。
 * 使用 data binding 将你的数据模型装配到你的视图中。
 * 使用 Karma 来创建和运行单元测试。
 * 使用 Protractor 来创建和运行端到端测试（[end to end tests](http://www.exforsys.com/tutorials/testing-types/end-to-end-testing.html)）。
 * 将应用逻辑从模板和控制层抽离出去。
 * 使用Angular services从服务端获取数据。
 * 使用 ngAnimate 创建应用动画效果。

此教程将按步骤引导你构建一个简单的应用，其中包括书写和运行单元测试与端到端测试。在每一个步骤的最后会有一些实验，提供些建议来帮助你更深入的学习Angularjs。

## 准备工作

