---
title: 使用D3js绘制力学图
description: 
date: 2015.07.12
tags:
- 博客
- D3js
- javascript
category: 日志
---

## D3.js简介
[D3.js][]是一个基于数据的文档操作javascript库，使用D3js能够大大简化对svg的繁琐操作，使用其提供的简单的API就可以制作许多酷炫的图表。

D3是一个开源项目，最新的源码请点击[这里](https://github.com/mbostock/d3)，作者是纽约时报的可视化编辑[Mike Bostock][]与他斯坦福大学的教授和学生，D3的全称是Data-Driven Documents。D3的应用十分广泛，经过[Github](https://github.com/)社区的不断完善和补充，目前已经成为了大数据的主流可视化工具之一，D3的速度非常快，能够处理大数据集以及相关的交互性动画行为，以下截图自D3的官网：

![D3 gallery](/img/D3js/d3 gallery.png)
更多的作品可以访问[这里](https://github.com/mbostock/d3/wiki/Gallery)。

## 如何安装D3
直接下载D3.js文件：[D3js](https://github.com/mbostock/d3/releases/download/v3.4.8/d3.zip)。解压后，直接在html文件中引用即可。或者直接在html中包含网络链接：

	<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>


## D3.js的兼容性
目前D3兼容Firefox, Chrome, Safari, Opera, IE9+, Android and iOS。

## 力学图（力导向图）的制作
_注：转自[馒头华华的专栏]，更多教程和例子请移步[馒头华华的专栏]。_

首先，介绍下力学图，力学图（也称为导向图，也有叫网络拓补图的，就是<span style="color:red;">通过排斥得到关系远近的结构</span>）在社交网络研究、信息传播途径等群体关系研究中应用非常广泛，它可以直观地反映群体与群体之间联系的渠道、交集多少，群体内部成员的联系强度等。

### 数据
初始数据如下：

        var nodes = [ { name: "GuiLin"    }, 
                      { name: "GuangZhou" },
                      { name: "XiaMen"    },
                      { name: "HangZhou"   },
                      { name: "ShangHai"   },
                      { name: "QingDao"    },
                      { name: "TianJin"    },
                      { name: "BeiJing"    },
                      { name: "ChangChun"  },
                      { name: "XiAn"       },
                      { name: "WuluMuQi"  },
                      { name: "LaSa"       },
                      { name: "ChengDu"    } ];
                     
        var edges = [  { source : 0  , target: 1 } ,
                       { source : 1  , target: 2 } ,
                       { source : 2  , target: 3 } ,
                       { source : 3  , target: 4 } ,
                       { source : 4  , target: 5 } ,
                       { source : 5  , target: 6 } ,
                       { source : 6  , target: 7 } ,
                       { source : 7  , target: 8 } ,
                       { source : 8  , target: 9 } ,
                       { source : 9  , target: 10 } ,
                       { source : 10 , target: 11 } ,
                       { source : 11 , target: 12 } ,
                       { source : 12 , target: 0 } ];
 
这里有顶点（nodes）和边（edges），顶点是一些城市名，边是两顶点之间的连线。用这些数据来做力学图是不合适的，比如不知道顶点绘制的位置等。需要先用布局（Layout）来转换数据。

### 布局（数据转换）

布局（Layout），即数据转化函数。力学图的布局定义如下：

    var force = d3.layout.force()
                            .nodes(nodes)
                            .links(edges)
                            .size([width,height])
                            .linkDistance(200)
                            .charge([-100])
                            .start();

其中，
 * d3.layout.force() 是力学图 Layout 的函数
 * nodes() 里放入顶点的数组
 * links() 里放入边的数组
 * size() 是作用域的大小
 * linkDistance() 用于设定两个顶点之间的长度
 * charge() 设定相互吸引（+）还是排斥（-），以及大小
 * start() 开始计算

调用这个函数后，我们来看看数据从什么转换成什么了：

顶点（转换前）：

![](/img/D3js/921.png)

顶点（转换后）：

![](/img/D3js/922.png)

转换后，多了一些数据。

其中,
 * index 在顶点数组里的索引号（新）
 * name 原顶点就有的属性（旧）
 * px, py 顶点上一个时刻的坐标（新）
 * x, y 顶点当前坐标（新）
 * weight 顶点权重（新）

再来看看边的数据：

边（转换前）：
![](/img/D3js/923.png)

转换后：
![](/img/D3js/924.png)

可以看到，边的两个成员变量变成了两个顶点的数据。

### 绘制
有了转换后的数据，就可以作图了。

    var svg_edges = svg.selectAll("line")
                            .data(edges)
                            .enter()
                            .append("line")
                            .style("stroke","#ccc")
                            .style("stroke-width",1);
        
        var color = d3.scale.category20();
                            
        var svg_nodes = svg.selectAll("circle")
                            .data(nodes)
                            .enter()
                            .append("circle")
                            .attr("r",10)
                            .style("fill",function(d,i){
                                return color(i);
                            })
                            .call(force.drag);

最后还需要一段代码，如下：

        force.on("tick", function(){
        
             svg_edges.attr("x1",function(d){ return d.source.x; });
             svg_edges.attr("y1",function(d){ return d.source.y; });
             svg_edges.attr("x2",function(d){ return d.target.x; });
             svg_edges.attr("y2",function(d){ return d.target.y; });
             
             svg_nodes.attr("cx",function(d){ return d.x; });
             svg_nodes.attr("cy",function(d){ return d.y; });
        });

tick 指的是时间间隔，也就是每一个时间间隔之后就刷新一遍画面，刷新的内容写在后面的无名函数 function中，函数中写上更新的内容即可。

来看看最终效果图，用鼠标拖拽看看：

<div id="d3Demo"></div>
<script>    
$(document).ready(function($){
    $('[data-toggle="popover"]').popover();

    var nodes = [ { name: "吉林"    }, 
                      { name: "广州" },
                      { name: "厦门"    },
                      { name: "杭州"   },
                      { name: "上海"   },
                      { name: "青岛"    },
                      { name: "天津"    },
                      { name: "北京"    },
                      { name: "长春"  },
                      { name: "西安"       },
                      { name: "乌鲁木齐"  },
                      { name: "拉萨"       },
                      { name: "成都"    } ];
                     
        var edges = [  { source : 0  , target: 1 } ,
                       { source : 1  , target: 2 } ,
                       { source : 2  , target: 3 } ,
                       { source : 3  , target: 4 } ,
                       { source : 4  , target: 5 } ,
                       { source : 5  , target: 6 } ,
                       { source : 6  , target: 7 } ,
                       { source : 7  , target: 8 } ,
                       { source : 8  , target: 9 } ,
                       { source : 9  , target: 10 } ,
                       { source : 10 , target: 11 } ,
                       { source : 11 , target: 12 } ,
                       { source : 12 , target: 0 } ];   
        
        var width = 800;
        var height = 500;
        var img_w = 50;
        var img_h = 50;
        
        var svg = d3.select("#d3Demo").append("svg")
                                .attr("width",width)
                                .attr("height",height);
        
        var force = d3.layout.force()
                            .nodes(nodes)
                            .links(edges)
                            .size([width,height])
                            .linkDistance(200)
                            .charge([-100])
                            .start();
                            
        var svg_edges = svg.selectAll("line")
                            .data(edges)
                            .enter()
                            .append("line")
                            .style("stroke","#ccc")
                            .style("stroke-width",1);
        
        var color = d3.scale.category20();
                            
        var svg_nodes = svg.selectAll("circle")
                            .data(nodes)
                            .enter()
                            .append("circle")
                            .attr("r",15)
                            .style("fill",function(d,i){
                                return color(i);
                            })
                            .attr("data-toggle", "popover")
                            .attr("data-content", function(d) {
                              return d.name;
                            })
                            .attr("data-container", "body")
                            .on("mouseenter", function() {
                                $(this).popover('show');
                            })
                            .on("mouseleave", function() {
                                $(this).popover('hide');
                            })
                            .call(force.drag);
                            
        force.on("tick", function(){
            
            // nodes.forEach(function(d, i) {
            //     d.x = d.x - img_w/2 < 0 ? img_w/2 : d.x ;
            //     d.x = d.x + img_w/2 > width ? width - img_w/2 : d.x ;
            //     d.y = d.y - img_h/2 < 0 ? img_h/2 : d.y ;
            //     d.y = d.y + img_h/2 > height ? height - img_h/2 : d.y ;
            // });

             svg_edges.attr("x1",function(d){ return d.source.x; });
             svg_edges.attr("y1",function(d){ return d.source.y; });
             svg_edges.attr("x2",function(d){ return d.target.x; });
             svg_edges.attr("y2",function(d){ return d.target.y; });
             
             svg_nodes.attr("cx",function(d){ return d.x; });
             svg_nodes.attr("cy",function(d){ return d.y; });
        });
});      
    
</script>

参考资料：
>[数据新闻]: http://djchina.org/2014/01/29/my-learning-experience-w-d3js/
>[馒头华华的专栏]: http://www.ourd3js.com/wordpress/
>[Mike Bostock]: http://bost.ocks.org/mike/
>[D3.js]: http://d3js.org/

[Duandun]: http://blog.chruyo.com "Duandun"
[数据新闻]: http://djchina.org/2014/01/29/my-learning-experience-w-d3js/
[馒头华华的专栏]: http://www.ourd3js.com/wordpress/
[Mike Bostock]: http://bost.ocks.org/mike/
[D3.js]: http://d3js.org/