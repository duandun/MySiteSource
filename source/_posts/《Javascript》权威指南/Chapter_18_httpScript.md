---
title: Chapter 18 脚本化http
description: 
date: 2016.03.20
tags:
- 博客
- 《javascript》权威指南
- javascript
category: 日志
---
## 18.1 使用XMLHttpRequest
一个HTTP请求由4部分组成：
* HTTP请求方法或“动作”
* 正在请求的URL
* 一个可选的请求头集合，其中可能包括身份验证信息
* 一个可选的请求主题

服务器返回的HTTP相应包含3部分：
* 一个数字和文字组成的状态吗，用来显示请求的成功和失败
* 一个响应头集合
* 响应主体

创建XMLHttpRequest对象之后，发起HTTP请求的下一步是调用XMLHttpRequest对象的open()方法去指定这个请求的两个必须部分：方法和URL
        
        request.open("GET",           // 开始一个HTTP GET请求
                        "data.csv")   // URL的内容

如果有请求头的话，请求进程的下个步骤就是设置它。例如，POST请求需要“Content-Type”头指定请求主题的MIME类型：

        request.setRequestHeader("Content-Type", "text/plain");

如果对相同的头调用setRequestHeader()多次，新值不会取代之前指定的值，相反，HTTP请求将包含这个头的多个副本或这个头将指定多个值。

XMLHttpRequest对象通常异步使用：发送请求后，send()方法立即返回，直到响应返回。为了在响应准备就绪时得到通知，必须监听XMLHttpRequest对象上的readystatechange事件。但为了理解这个事件类型，你必须理解readyState属性。
readyState是一个整数，它指定了HTTP请求的状态：
 
* UNSENT     0       open()尚未调用
* OPENED     1       open()已调用
* HEADERS_RECEIVED     2      接收到头信息
* LOADING         3        接收到响应主体
* DONE        4         响应完成

理论上，每次readyState属性改变都会触发readystatechange事件。

        // 获取HTTP响应的onreadystatechange
        // 发出一个HTTP GET请求以获得指定URL的内容
        // 当响应成功到达，验证它是否是纯文本
        // 如果是，把它传递给指定回调函数
        function getText(url, callback) {
            var request = new XMLHttpRequest();
            request.open("GET", url);
            request.onreadystatechange = function() {
                // 如果请求完成，则它是成功的
                if(request.readyState === 4 && request.status === 200) {
                    var type = request.getResponseHeader("Content-Type");
                    if (type.match(/^text/)) {   // 确保响应是文本
                        callback(request.responseText);    // 把它传递给回调函数
                    }
                }
            };
            request.send(null);     // 立即发送请求
        }














