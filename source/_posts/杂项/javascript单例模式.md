---
title: javascript单例模式
description: 
date: 2016.03.19
tags:
- 博客
- 杂项
- singleton
- javascript
category: 日志
---
在传统开发工程师眼里，单例就是保证一个类只有一个实例，实现的方法一般是先判断实例存在与否，如果存在直接返回，如果不存在就创建了再返回，这就确保了一个类只有一个实例对象。在JavaScript里，单例作为一个命名空间提供者，从全局命名空间里提供一个唯一的访问点来访问该对象。
在JavaScript里，实现单例的方式有很多种，其中最简单的一个方式是使用对象字面量的方法，其字面量里可以包含大量的属性和方法：
        
        var mySingleton = {
            property1: "something",
            property2: "something else",
            method1: function () {
                console.log('hello world');
            }
        };

如果以后要扩展该对象，你可以添加自己的私有成员和方法，然后使用闭包在其内部封装这些变量和函数声明。只暴露你想暴露的public成员和方法，样例代码如下：

        var mySingleton = function () {

            /* 这里声明私有变量和方法 */
            var privateVariable = 'something private';
            function showPrivate() {
                console.log(privateVariable);
            }

            /* 公有变量和方法（可以访问私有变量和方法） */
            return {
                publicMethod: function () {
                    showPrivate();
                },
                publicVar: 'the public can see this!'
            };
        };

        var single = mySingleton();
        single.publicMethod();  // 输出 'something private'
        console.log(single.publicVar); // 输出 'the public can see this!'

上面的代码很不错了，但如果我们想做到只有在使用的时候才初始化，那该如何做呢？为了节约资源的目的，我们可以另外一个构造函数里来初始化这些代码，如下：

        var Singleton = (function () {
            var instantiated;
            function init() {
                /*这里定义单例代码*/
                return {
                    publicMethod: function () {
                        console.log('hello world');
                    },
                    publicProperty: 'test'
                };
            }

            return {
                getInstance: function () {
                    if (!instantiated) {
                        instantiated = init();
                    }
                    return instantiated;
                }
            };
        })();

        /*调用公有的方法来获取实例:*/
        Singleton.getInstance().publicMethod();

