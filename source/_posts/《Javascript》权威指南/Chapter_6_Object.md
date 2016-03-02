---
title: Chapter 6 对象
description: 
date: 2016.03.02
tags:
- 博客
- 《javascript》权威指南
- javascript
category: 日志
---

## 6.1.4 Object.create()
传入参数null来创建一个没有原型的新对象，但是通过这种方式创建的对象不会继承任何东西，甚至不包括基础方法，比如toString(),也就是说，它将不能和"+"运算符一起正常工作：
    
    var o2 = Object.create(null);    // o2不继承任何属性和方法

如果想创建一个普通的空对象（比如通过{}或new Object()创建的对象），需要传入Object.prototype:

    var o3 = Object.create(Object.prototype);    // o3和{}和new Object()一样

可以通过任意原型创建新对象（换句话说，可以使任意对象可继承）。在ECMAScript 3中可以使用代码来模拟原型继承：

    // inherit(): 返回了一个继承自原型对象p的属性的新对象
    // 这里使用ECMAScript 5中的Object.create()函数（如果有的话）
    // 如果不存在Object.create()，则使用其他方法
    function inherit(p) {
        if (p == null) throw TypeError();
        if (Object.create) {
            return Object.create(p);
        }
        var t = typeof p;
        if (t !== "Object" && t !== "function") {
            throw TypeError();
        }
        function f() {};
        f.prototype = p;
        return new f();
    }

## 6.2.2 继承
在Javascript中，只有在查询属性时候才会体会到继承的存在，而设置属性则与继承无关，这是Javascript中的一个重要特性，该特性可以让程序员有选择地覆盖（override）继承的属性。

    var unitcircle = { r: 1 };   
    var c = inherit(unitcircle);  // c继承属性r
    c.x = 1, c.y = 1;
    c.r = 2;                      // c覆盖继承来的属性
    unitcircle.r;                 // => 1，原型对象没有修改

## 6.3 删除属性
delete只是断开属性和宿主对象的联系，而不会去操作属性中的属性（<span style="color: #ff6026;">例如 a = {p:{x:1}}; b = a.p; delete a.p; 执行这段代码之后b.x的值依然是1。由于已经删除的属性的引用依然存在，因此在Javascript的某些实现中，可能因为这种不严谨的代码而造成内存泄漏，所以在销毁对象的时候，要遍历属性中的属性，依次删除。</span>）：

    delete book.author;    // book中不再有属性author
    delete book["main title"];    // book也不再有属性"main title"

delete运算符只能删除自有属性，不能删除继承属性。

## 6.4 检测属性
对象的 __hasOwnProperty()__ 方法用来检测给定的名字是否是对象的自有属性。对于继承属性它将返回false。
 __propertyIsEnumerable()__ 是 __hasOwnProperty()__ 的增强版，只有检测到是自有属性并且这个属性的可枚举性为true时候它才返回true。
in运算符用以检测对象的自有属性或继承属性。

## 6.5 枚举属性
for/in循环可以在循环体中遍历对象中所有可枚举的属性（包括自有属性和继承的属性）。除了for/in之外，ECMAScript 5中定义了两个用以枚举属性名称的函数。一个是 __Object.keys()__ ，它返回一个数组，这个数组由对象中可枚举的自有属性的名称组成。第二个是 __Object.getOwnPropertyNames()__ ，它和 __Object.keys()__ 类似，只是它返回对象的所有自有属性的名称，而不仅仅是可枚举的属性。ECMAScript 3中是无法实现类似函数的。

## 6.6 属性getter和setter
当程序查询存取器属性的值时，Javascript调用__getter__方法（无参数），这个方法的返回值就是属性存取表达式的值，当程序设置一个存取器属性的值时，javascript调用__setter__方法，将赋值表达式右侧的值当做参数传入__setter__。
如果属性同时具有__getter__和__setter__方法，那么它是一个读/写属性。如果它只有__getter__方法，那么它是一个只读属性。如果它只有__setter__方法，那么它是一个只写属性（数据属性中有一些例外），读取只写属性总是返回undefined。
一些使用存取器属性的简单例子：

    // 这个对象产生严格自增的序列号
    var serialnum = {
        // 这个数据属性包含下一个序列号
        // $符号暗示这个属性是一个私有属性
        $n: 0,

        // 返回当前值，然后自增
        get next() { return this.$n++; },

        // 给n设置新的值，但只有当它比当前值大时才设置成功
        set next(n) {
            if ( n >= this.$n ) this.$n = n;
            else throw "序列号的值不能比当前值小";
        }
    };


    // 这个对象可以返回一个随机数
    var random = {
        get octet() { return Math.floor(Math.random()*256); },
        get uint16() { return Math.floor(Math.random()*65536); },
        get int16() { return Math.floor(Math.random()*65536)-32768; }
    };

## 6.7 属性的特性
数据属性的四个特性分别为它的值(value)，可写性(writable)，可枚举性(enumerable)和可配置性(configurable)。
通过调用__Object.getOwnPropertyDescriptor()__ 可以获得某个对象特定属性的属性描述符：

    // 对于继承属性和不存在的属性，返回undefined
    Object.getOwnPropertyDescriptor({}, "x");    // undefined, 没有这个属性
    Object.getOwnPropertyDescriptor({}, "toString");   // undefined, 继承属性

要想获得继承属性的特性，需要遍历原型链（Object.getPrototypeOf()）。
要想设置属性的特性，或者想让新建属性具有某种特性，则需要调用__Object.defineProperty()__ ：

    var o = {};
    Object.defineProperty(o, "x", { value: 1,
                                    writable: true,
                                    enumerable: true,
                                    configurable: true});

如果要同时修改或创建多个属性，则需要使用__Object.defineProperties()__。

    var p = Object.defineProperties({}, {
            x: {value: 1, writable: true, enumerable: false, configurable: true},
            y: {value: 2, writable: false, enumerable: false, configurable: true}
        });





