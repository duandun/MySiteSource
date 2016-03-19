---
title: null与undefined的区别
description: 
date: 2016.03.19
tags:
- 博客
- 杂项
- javascript
category: 日志
---
在JavaScript中存在这样两种原始类型:Null与Undefined。这两种类型常常会使JavaScript的开发人员产生疑惑，在什么时候是Null，什么时候又是Undefined?

Undefined类型只有一个值，即undefined。当声明的变量还未被初始化时，变量的默认值为undefined。
Null类型也只有一个值，即null。null用来表示尚未存在的对象，常用来表示函数企图返回一个不存在的对象。

        var oValue;  
        alert(oValue == undefined); //output "true"  

这段代码显示为true,代表oVlaue的值即为undefined，因为我们没有初始化它。

        alert(null == document.getElementById('notExistElement'));  

当页面上不存在id为"notExistElement"的DOM节点时，这段代码显示为"true"，因为我们尝试获取一个不存在的对象。

        alert(typeof undefined); //output "undefined"  
        alert(typeof null); //output "object"  

第一行代码很容易理解，undefined的类型为Undefined；第二行代码却让人疑惑，为什么null的类型又是Object了呢？其实这是JavaScript最初实现的一个错误，后来被ECMAScript沿用下来。在今天我们可以解释为，null即是一个不存在的对象的占位符，但是在实际编码时还是要注意这一特性。

        alert(null == undefined); //output "true"  

ECMAScript认为undefined是从null派生出来的，所以把它们定义为相等的。但是，如果在一些情况下，我们一定要区分这两个值，那应该怎么办呢？可以使用下面的两种方法。

        alert(null === undefined); //output "false"  
        alert(typeof null == typeof undefined); //output "false"  

使用typeof方法在前面已经讲过，null与undefined的类型是不一样的，所以输出"false"。而===代
表绝对等于，在这里null === undefined输出false。
