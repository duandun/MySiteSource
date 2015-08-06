---
title: 使用Html5完成客户端扫描识别二维码
date: 2015.08.06
tags:
- 博客
- html5
- javascript
- qrcode
category: 日志
---
最近由于项目的需要，领导想要做一个类似授权模块的东西，通过这个模块，允许用户授权给第三方应用或是网站来访问其存储在其他网站（应用）上的特定信息，这种授权方式被成为 [oAuth2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)，被大量应用在微博，微信等应用和网站，论坛中。在开发过程中，领导想要通过扫二维码的方式来进行令牌（token）的获取和身份的认证，为简化操作和开发过程，决定使用 Html5 来实现这一扫码功能。
<!--more-->
## 准备工作
* 两部测试机：iphone5s + galaxy s4
* 测试浏览器：Chrome in Android, UC in Android, Safari in iphone5s
* web环境：Nodejs + bootstrap3 
* 工具库：jsqrcode, jquery-qrcode

由于手头就两部手机，所以这两部就分别作为IOS和Android测试机器。对于 html5 中新增标签和 api 的浏览器支持情况，可以访问如下网站查询： http://caniuse.com/ 。

由于研究时间有限，所以就先找来支持情况比较好的 Chrome 和 UC 浏览器作为测试浏览器，在iphone5s中则使用原生浏览器 Safari 。

扫码工作主要在前端进行，后端主要进行数据的处理和路由发送信息，所以没有必要再去搭建一个整套的 MVC 框架来运行应用（确实我对后端的技术也不是很了解），这里采用比较偷懒的自动搭建了一个 nodejs web环境，用来模拟服务器端。

样式采用了[bootstrap3](http://v3.bootcss.com/)框架（只是简单应用了一些栅格系统和按钮，让这个demo不会显得太挫）。

[jsqrcode](https://github.com/LazarSoft/jsqrcode) 是 [zxing qrcode scanner](https://github.com/zxing/zxing) 的一个javascript移植，应用这个库就可以在客户端进行二维码的识别工作了！[jquery-qrcode](http://jeromeetienne.github.io/jquery-qrcode/) 是 [jquery](http://jquery.com/) 的一个插件，用来生成二维码的工具库。

## 使用express-generator初始化一个项目
首先要安装 `express-generator` 
使用 `npm` 来进行安装：
    
    sudo npm install -g express-generator
windows 用户不需要键入 `sudo` 

安装完 `express-generator` 后，执行以下命令：

    express ejs myapp
这样就在当前目录下生成了一个名叫myapp 的 express项目了。这个项目以 `ejs` 作为模板，关于 `ejs` 的使用，请移步： http://www.embeddedjs.com/ 
该项目的目录结构如下：
![](/img/html5/tree_content.png)

对于其具体的运行过程和各个文件的作用，请移步 [nodejs社区](https://cnodejs.org/) 进行学习，在这里，我们输入以下命令来安装相关的依赖包

    npm install
在下载完相关包后，键入命令：

    npm start
就可以看到项目已经开始运行了。
![](/img/html5/npm_start.png)

这时候，在浏览器中输入网址 http://localhost:3000/ 便能看到网站正在运行了！
![](/img/html5/running.png)

## 言归正传
如今二维码已经深入到我们的生活当中，支付宝中的二维码支付，微信中的“扫一扫”，手机应用app的下载也有很多采用了二维码扫描的方式。相比较于过去经常使用的条形码，二维码拥有很多优势：

* 数据容量大（一维条形码仅能最多存储30个字符左右，二维码则最多可容纳1850个大写字母或2710个数字或1108个字节，或500多个汉字）
* 超越了字母数字的限制（一维条形码仅能包含字母和数字，而二维码可以包含汉字，图片，声音，签字，指纹等信息）
* 条码相对尺寸比较小
* 具有抗损伤能力（二维码在因为穿孔、污损等引起局部损坏时，照样可以被正确识别）
* 译码可靠性高（二维码比普通一维条形码译码错误率百万分之二要低得多，误码率不超过千万分之一）
* 可以引入加密措施
* 符号性状，尺寸大小可变

### 说了这么多，也该开始正题了
首先使用 `bower` 来下载对应要使用的工具库，配置 `.bowerrc` ，在项目根目录下创建一个叫 `.bowerrc` 的文件，输入以下代码：
    
    {
        "directory": "public/libs"
    }
这句话表示你的静态文件所在的目录。

在终端中输入如下命令：

    bower install jsqrcode, jquery-qrcode, bootstrap --save
这样所需要的依赖库就都下载完毕了。

在项目目录下的 `views` 目录下创建文件 `qrcode.ejs` ，并在 `routes` 目录下的 `index.js` 文件中添加如下语句：

    router.get('/qr', function(req, res, next) {
        res.render('qrcode');
    });
这样，在浏览器中输入 http://localhost:3000/qr ，便能跳转到 `qrcode.ejs` 页面并渲染。

我们的主要程序都是写在 `qrcode.ejs` 文件中。
由于是在手机端的 webapp， 所以需要添加如下语句来使得页面适应手机端的浏览器

    <meta name="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no,minimal-ui">

加入用于解析二维码的js库文件（一大堆）

    <script type="text/javascript" src="/js/grid.js"></script>
    <script type="text/javascript" src="/js/version.js"></script>
    <script type="text/javascript" src="/js/detector.js"></script>
    <script type="text/javascript" src="/js/formatinf.js"></script>
    <script type="text/javascript" src="/js/errorlevel.js"></script>
    <script type="text/javascript" src="/js/bitmat.js"></script>
    <script type="text/javascript" src="/js/datablock.js"></script>
    <script type="text/javascript" src="/js/bmparser.js"></script>
    <script type="text/javascript" src="/js/datamask.js"></script>
    <script type="text/javascript" src="/js/rsdecoder.js"></script>
    <script type="text/javascript" src="/js/gf256poly.js"></script>
    <script type="text/javascript" src="/js/gf256.js"></script>
    <script type="text/javascript" src="/js/decoder.js"></script>
    <script type="text/javascript" src="/js/qrcode.js"></script>
    <script type="text/javascript" src="/js/findpat.js"></script>
    <script type="text/javascript" src="/js/alignpat.js"></script>
    <script type="text/javascript" src="/js/databr.js"></script>

由于需要在网页中调用手机摄像头，这样就需要使用 html5 中提供的一些新的 api，在这里需要使用 `getUserMedia` 和 `MediaStreamTrack` 两个函数。其中， `getUserMedia` 可以获取当前设备的摄像头和音频设备，`MediaStreamTrack` 用来获取当前设备音频和视频源（如获取手机多个摄像头的`Id`）。


--太累了，明天再接着写吧。。。--



