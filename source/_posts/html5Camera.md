---
title: 使用Html5完成客户端扫描识别二维码
date: 2015.08.06
tags:
- 分享
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

### 实现原理
使用 `video` 标签来显示当前设备的视频图像，将图像绘制在 `canvas` 标签中，再使用二维码解析库解析 `canvas`。

代码如下：   

    <!DOCTYPE HTML>  
    <html>  
    <head>  
    <title>授权页面</title>  
    <meta charset="utf-8">  
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no,minimal-ui">
    <meta name="author" content="duandun">
    <link rel='stylesheet' href='/libs/bootstrap/dist/css/bootstrap.css' />
    <script type="text/javascript" src="/libs/jquery/dist/jquery.js"></script>
    <script type="text/javascript" src="/libs/bootstrap/dist/js/bootstrap.js"></script>
     <!-- <script type="text/javascript" src="/libs/angularjs/angular.js"></script> -->

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

    <style>
        #qr-canvas {
            position: relative;
            top: -200px;
            z-index: 2000;
        }
        .row {
            margin-bottom: -20px;
        }
    </style>
    </head>  
    <body class="container text-center" >  
    <div class="row" >
        <video id="video" width="300" height="300" autoplay></video>        
    </div>
    <canvas id="qr-canvas" width="100" height="100"></canvas>
    <div class="row">
        <div class="col-xs-6">
            <canvas id="canvasA" width="100" height="100" ></canvas>
            <input type="hidden" id="inputA"  >
        </div>
        <div class="col-xs-6">
            <canvas id="canvasB" width="100" height="100" ></canvas>
            <input type="hidden" id="inputB" >
        </div>
    </div>

    <div class="row">
        <div class="col-xs-4">
            <a type="button" value="授权机" id="btn1" class="btn btn-primary" href="javascript:;">授权机</a>
        </div>
        <div class="col-xs-4">
            <a type="button" id="send" class="btn btn-primary" href="javascript:;" onclick="javascript:send();" disabled="disabled">登陆</a>
        </div>
        <div class="col-xs-4">
            <a type="button" value="被授权机" id="btn2" class="btn btn-primary" href="javascript:;">被授权机</a>  
        </div>   
    </div>
    <script type="text/javascript"> 

    // Put event listeners into place

    function send() {
    alert($("#inputA").val() + "   " + $("#inputB").val());
    }

    window.addEventListener("DOMContentLoaded", function() {

    // Grab elements, create settings, etc.
    var canvas = document.getElementById("qr-canvas"),
        context = canvas.getContext("2d"),
        video = document.getElementById("video"), 
        videoArray = [],
        btn1 = document.getElementById("btn1"),
        btn2 = document.getElementById("btn2"),
        canvasA = document.getElementById("canvasA"),
        canvasB = document.getElementById("canvasB"),
        errBack = function(error) {
            console.log("Video capture error: ", error.code); 
        };

    // 获取设备的媒体数据
    navigator.getUserMedia = navigator.getUserMedia ||
        navigator.webkitGetUserMedia || navigator.mozGetUserMedia;

    // 将canvas图像转换为黑白图片
    function imageConvertToGray(ctx){ 
        var length = canvas.width * canvas.height,
            imageData = ctx.getImageData(0, 0, canvas.width, canvas.height); 
        for (var i = 0; i < length * 4; i += 4) { 
            var myRed = imageData.data[i]; 
            var myGreen = imageData.data[i + 1]; 
            var myBlue = imageData.data[i + 2]; 
            myGray = parseInt((myRed + myGreen + myBlue) / 3); 
            imageData.data[i] = myGray; 
            imageData.data[i + 1] = myGray; 
            imageData.data[i + 2] = myGray; 
        } 
        ctx.putImageData(imageData, 0, 0); 
    } 

    // 获取设备的摄像头信息id
    function gotSources(sourceInfos) {
      for (var i = 0; i !== sourceInfos.length; ++i) {
        var sourceInfo = sourceInfos[i];
        if (sourceInfo.kind === 'video') {
          videoArray.push(sourceInfo.id);
          // alert(videoArray);
        } else {
          console.log('Some other kind of source: ', sourceInfo);
        }
      }
      var constraints = {
            video: {
              optional: [{
                sourceId: videoArray[1]
              }]
            }
        };
      start(constraints);
    }

    // 进行扫描任务
    function scan() {
        if (typeof MediaStreamTrack === 'undefined' ||
            typeof MediaStreamTrack.getSources === 'undefined') {
            alert('This browser does not support MediaStreamTrack.\n\nTry Chrome.');
            var constraints = {
                video: true
            };
            start(constraints);
        } else {
          MediaStreamTrack.getSources(gotSources);
        }
     }

     // 
    function successCallback(stream) {
      window.stream = stream; // make stream available to console
      video.src = window.URL.createObjectURL(stream);
      video.play();
    }

    function errorCallback(error) {
      console.log('navigator.getUserMedia error: ', error);
    }

    // 扫描二维码事件
    function recEvent(canvasDis) {
        var $input, btn;
        if (canvasDis.getAttribute("id")==="canvasA") {
                $input = $("#inputA");
                btn = document.getElementById("btn1");
        } else if (canvasDis.getAttribute("id")==="canvasB") {
                $input = $("#inputB");
                btn = document.getElementById("btn2");
        }
        contextDis = canvasDis.getContext("2d");
        // 每20毫秒取一个图像并进行二维码解析
        var i = setInterval(function() {
              //  context.drawImage(video, 140,220,200,200,0,0,200,200);
              context.drawImage(video, 140,220,300,300,0,0,200,200);
                //为提高速度，可以不转换为黑白图像
               // imageConvertToGray(context);
                 try{
                     qrcode.callback = function(data) {
                        alert(data);
                        console.log('the data is : ' + data);
                        
                        // 解析成功后，将解析得到的数据传入到对应隐藏域中
                        $input.val(data);

                        if ($("#inputA").val() && $("#inputB").val()) {
                            $("#send").removeAttr("disabled");
                        }
                        video.src = '';
                        video.pause();
                        contextDis.putImageData(context.getImageData(0,0,canvas.width, canvas.height),0,0);
                        context.clearRect(0,0,200,200); 
                        btn.style.backgroundColor = '#CC6633';
                        btn.style.borderColor = '#CC6633';

                        };
                     qrcode.decode();          
                      } catch(e) {           
                        console.log('unable to read qr code');
                    }
            }, 20);
    }

    // 绑定授权机按钮事件
    video.addEventListener('pause', function() {
            clearInterval(i);
    });

    // 绑定授权机按钮事件
    btn1.addEventListener('click', function() {
        $(video).one('play', function() {
            recEvent(canvasA);
        });
        this.setAttribute('style', '');
        scan();
    });

    // 绑定被授权机按钮事件
    btn2.addEventListener('click', function() {
        $(video).one('play', function() {
            recEvent(canvasB);
        });
          this.setAttribute('style', '');
         scan();
    });

    function start(constraints) {
      if (!!window.stream) {
        video.src = null;
        window.stream.stop();
      }
        navigator.getUserMedia(constraints, successCallback, errorCallback);
    }
    }, false);

    </script>  
    </body>  
    </html>  



