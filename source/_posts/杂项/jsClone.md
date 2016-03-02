---
title: js深拷贝
description: 
date: 2016.03.02
tags:
- 博客
- javascript
category: 日志
---
## JS深拷贝

    var cloneObj = function(obj){
        var str, newobj = obj.constructor === Array ? [] : {};
        if(typeof obj !== 'object'){
            return;
        } else if(window.JSON){
            str = JSON.stringify(obj), //系列化对象
            newobj = JSON.parse(str); //还原
        } else {
            for(var i in obj){
                newobj[i] = typeof obj[i] === 'object' ? 
                cloneObj(obj[i]) : obj[i]; 
            }
        }
        return newobj;
    };

著作权归作者所有。
商业转载请联系作者获得授权，非商业转载请注明出处。
作者：青大虫
链接：http://www.zhihu.com/question/23031215/answer/31944721
来源：知乎