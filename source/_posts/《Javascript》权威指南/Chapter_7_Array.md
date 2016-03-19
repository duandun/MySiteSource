---
title: Chapter 7 数组
description: 
date: 2016.03.19
tags:
- 博客
- 《javascript》权威指南
- javascript
category: 日志
---
## 7.2 数组元素的读和写
当使用小于2^32的非负整数作为属性名时数组会自动维护其length属性值。
也可以使用负数或非整数来索引数组。在这种情况下，数值转换为字符串，字符串作为属性名来用。
        
        a[-1.234] = true;  // 这将创建一个名为"1.234"的属性
        a["1000"] = 0;     // 这是数组的第1001个元素
        a[1.000]           // 和a[1]相等

事实上数组索引仅仅是对象属性名的一种特殊类型，这意味着javascript数组没有“越界”错误的概念。当试图查询任何对象中不存在的属性时，不会报错，只会得到Undefined值。
## 7.3 稀疏数组
稀疏数组就是包含从0开始的不连续索引的数组。通常，数组的Length属性值代表数组中元素的个数。如何数组是稀疏的，length属性值大于元素的个数。可以用Array()构造函数或简单地指定数组的索引值大于当前的数组长度来创建稀疏数组。

        a = new Array(5);    // 数组没有元素，但是a.length是5
        a = [];              // 创建一个空数组，length = 0
        a[1000] = 0;         // 赋值添加一个元素，但是设置Length为1001

注意，当在数组直接量中省略值时不会创建稀疏数组。省略的元素在数组中是存在的，其值为undefined。这和数组元素根本不存在是有一定微妙的区别的。可以用in操作符检测两者之间的区别：

        var a1 = [,,,];     // 数组是[undefined, undefined, undefined]
        var a2 = new Array(3);    // 该数组根本没有元素
        0 in a1             // => true: a1在索引0处有一个元素
        0 in a2             // => false: a2在索引0处没有元素

## 7.4 数组长度
每个数组都有一个Length属性，就是这个属性使其区别于常规的javascript对象。
## 7.5 数组元素的添加和删除
可以像删除对象属性一样使用delete运算符来删除数组元素：
        
        a = [1,2,3];
        delete a[1];      // a 在索引1的位置不再有元素
        1 in a;           // => false, 数组索引1并未在数组中定义
        a.length          // => 3: delete操作并不影响数组长度

## 7.6 数组的遍历
遍历数组的时候，要想排除null、undefined和不存在的元素，如下：
        
        for(var i = 0; i < a.length; i++) {
            if(!a[i]) continue;   // 跳过null、undefined和不存在的元素
            // 循环体
        }

可以使用for/in循环处理稀疏数组。循环每次将一个可枚举的属性名（包括数组索引）赋值给循环变量 。不存在的索引将不会遍历到：
        
        for(var index in sparseArray) {
            var value = sparseArray[index];
        }

## 7.8 数组的函数
### 7.8.6 splice()
Array.splice()方法是在数组中插入或删除元素的通用方法。
splice()的第一个参数制定了插入和（或）删除的起始位置。第二个参数制定了应该从数组中删除的元素的个数。如果省略第二个参数，从起始点开始到数组结尾的所有元素都将被删除。splice()返回一个由删除元素组成的数组，或者如果没有删除元素就返回一个空数组。例如：
        
        var a = [1,2,3,4,5,6,7,8];
        a.splice(4);  // 返回[5,6]; a是[1,2,3,4]
        a.splice(1,2);  // 返回[2,3]; a是[1,4]
        a.splice(1,1);  // 返回[4]; a是[1]

splice()的前两个参数指定了需要删除的数组元素。紧随其后的任意个数的参数指定了需要插入到数组中的元素，从第一个参数指定的位置开始插入。例如：

        var a = [1,2,3,4,5];
        a.splice(2,0,'a','b');   // 返回[]; a是[1,2,'a','b',3,4,5]
        a.splice(2,2,[1,2],3);   // 返回['a','b']; a是[1,2,[1,2],3,3,4,5]

### 7.9.2 map()
map()方法将调用的数组的每个元素传递给指定的函数，并返回一个数组，它包含该函数的返回值。例如：

        a = [1,2,3];
        b = a.map(function(x) {return x*x; });   // b是[1,4,9]

### 7.9.5 reduce()和reduceRight()
reduce()和reduceRight()方法使用指定的函数将数组元素进行组合，生成单个值。这在函数式编程中是常见的操作，也可以称为“注入”和“折叠”。例如：

        var a  = [1,2,3,4,5];
        var sum = a.reduce(function(x, y) {return x + y}, 0);     // 数组求和
        var product = a.reduce(function(x, y) {return x*y}, 1);    // 数组秋积
        var max = a.reduce(function(x, y) { return (x > y)?x:y; });   // 求最大值

reduce()需要两个参数。第一个是执行化简操作的函数。化简函数的任务就是用某种方法把两个值组合或化简为一个值，并返回化简后的值。在上述例子中，函数通过加法，乘法或取最大值的方法组合两个值。第二个（可选）的参数是一个传递给函数的初始值。
reduceRight()的工作原理和reduce()一样，不同的是它按照数组索引从高到低（从右到左）处理数组。

## 7.10 数组类型
在 ECMAScript 5 中，可以使用Array.isArray()函数来做数组类型的判断：
        
        Array.isArray([]);   // => true
        Array.isArray({});   // => false

在ECMAScript 3 中isArray()函数的代码可以这样写：

        var isArray = Function.isArray || function(o) {
            return typeof o === "object" && 
            Object.prototype.toString.call(o) === "[object Array]";
        };














