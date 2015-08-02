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
 * 通过使用客户端的 data binding 来绑定动态视图的数据，这样数据就能够实时地将用户的操作反应出来
 * Angular可以在不借助DOM操作的情况下同步你视图的变化
 * 使用 [Karma](http://karma-runner.github.io/0.13/index.html) 和 [Protractor](http://www.protractortest.org) 使你的测试变得更加简单，高效
 * Angular使用依赖注入和大量的服务来创建通用的模块，这将使构建代码更加容易

当你完成了这个教程后，你将会get如下技能：
 * 制作一个适用于所有流行浏览器的动态网页应用
 * 使用 data binding 将你的数据模型装配到你的视图中
 * 使用 Karma 来创建和运行单元测试
 * 使用 Protractor 来创建和运行端到端测试（[end to end tests](http://www.exforsys.com/tutorials/testing-types/end-to-end-testing.html)）
 * 将应用逻辑从模板和控制层抽离出去
 * 使用Angular services从服务端获取数据
 * 使用 ngAnimate 创建应用动画效果

此教程将按步骤引导你构建一个简单的应用，其中包括书写和运行单元测试与端到端测试。在每一个步骤的最后会有一些实验，提供些建议来帮助你更深入的学习Angularjs。

## 准备工作
本教程使用 [Git][Git] 作为代码版本控制工具，对于Git，你只需要懂得如何进行安装和一些简单的git命令即可。

### 安装 Git
你可以从Git的官方网站上下载 http://git-scm.com/download。 当安装完Git后，就可以使用 `git` 的命令行工具了。在本教程中，你将会频繁使用到如下两个命令：
 * `git clone ...` ：克隆一个远程项目到你本地
 * `git checkout ...` ：检出一个 `branch` 或是某个具体的版本

### 下载 angular-phonecat
在终端运行如下语句来克隆 Github 上的 [angular-phonecat repository][angular-phonecat repository] ：
    
    git clone --depth=14 https://github.com/angular/angular-phonecat.git

这条命令在你的当前目录下创建了 `angular-phonecat` 项目。
 > `--depth=14` 选项告知Git仅下载最新的14次commits，这会让下载的文件更少，速度也就相应提高了。

切换到 `angular-phonecat` 的目录下。

    cd angular-phonecat

确保在接下来的操作中，你都是在目录 `angular-phonecat` 下。

### 安装 Node.js
Node.js的官网下载地址如下： http://nodejs.org/download/ 。
检查你所安装的 Node.js 版本：

    node --version

在 Debian 系统中，`node` 会有命名冲突。建议安装 `nodejs-legacy` 。

    apt-get install nodejs-legacy npm 
    nodejs --version
    npm --version

 > 如果你想在本地环境中运行多个版本的 node.js, 你可以安装 [Node Version Manager (nvm)][nvm] 。

安装完 Node.js 后，就可以下载依赖包了：
    
    npm install

这条命令会下载以下几个工具包到 `node_modules` 文件夹下：
 * [Bower](http://bower.io/) - 代码包管理工具
 * [Http-Server](https://github.com/nodeapps/http-server) - 小巧的静态网页服务端
 * [Karma](https://github.com/karma-runner/karma) - 单元测试环境
 * [Protractor](https://github.com/angular/protractor) - 端到端测试环境

执行 `npm install` 语句会自动使用 bower 来下载Angular框架到 `app/bower_components` 文件夹下。
项目已经预先定义好了许多npm脚本用来执行一些在开发中通用的功能：
 * `npm start`：运行一个本地的web-server
 * `npm test`：开始 Karma 单元测试
 * `npm run protractor`：开始 Protractor 的端到端测试
 * `npm run update-webdriver`：安装 Protractor 所需要的驱动

### 安装辅助工具
安装bower工具
    
    sudo npm install -g bower

在windows系统中，请忽略 sudo
运行bower工具：

    bower install

### 运行开发环境

[nvm]:https://github.com/creationix/nvm
nvm: https://github.com/creationix/nvm
[angular-phonecat repository]:https://github.com/angular/angular-phonecat 
angular-phonecat repository: https://github.com/angular/angular-phonecat
[Git]:http://git-scm.com 
Git: http://git-scm.com