# JsPlumb.js使用总结
@[元素连线|元素拖放|事件绑定]
        jsPlumb是一个强大的JavaScript连线库，提供html元素的拖放、连线等功能，可绘制不同类型、样式的连线，适用于开发web页面的图表、建模工具等。除了Dom对象，同时支持jquery对象，也可以与jquery-ui结合使用。我没有使用jquery-ui的方法来实现拖放效果，jsPlumb本身也提供相关方法，参数与jquery-ui非常类似。
        
[TOC]

## 官网
>github上提供了多种类型连线的demo，因为直接看英文的官方文档比较难读，个人偏向的学习曲线是，初次使用建议先搜一两篇资料看个大概（有的翻译的较生硬）需要时结合官方文档https://jsplumbtoolkit.com/community/doc/home.html ；然后看gitHub上的demo，碰到具体的方法、参数设置等不清楚可到官网查找api文档；之后就开始在你的项目中使用吧。
>tips： 网上的一些资料由于年代较早，我们的jsPlumb一直保持着版本更新，若引用的新版本js，有的方法已经弃用，在api中都能找到替代方法，或者到其更新日志（https://jsplumbtoolkit.com/community/doc/changelog.html ）中去查找关键字。

官方网站：https://jsplumbtoolkit.com/ 
gitHub项目地址：https://github.com/jsplumb/jsPlumb 

网络资料：
文档翻译类：
https://segmentfault.com/a/1190000003826470
http://www.cnblogs.com/leomYili/p/6346526.html 
实用总结类：

                      

## 开始使用
jsPlumb官网提供社区版和toolkit版（付费），下载社区版js并在页面中引入。jsPlumb没有额外的依赖。

    <script src="jsplumb.js "></script>

jsPlumb必须等到DOM加载完成之后使用：
    
    jsPlumb.bind("ready",function(){
        ...
        // jsPlumb相关初始化代码
        ...
    });
    //or
    ...
    jsPlumb.ready(function(){  //简写
        ...
        // jsPlumb相关初始化代码
        ...
    });

jsPlumb默认注册在浏览器的window对象中，为整个页面提供了一个静态实例(jsPlumb)可直接使用，你也可以用getInstance方法创建一个单独的实例。即你可以直接使用对象[jsPlumb]或[myInstance]来调用jsPlumb的方法：

    var myInstance = jsPlumb.getInstance();

设置默认配置（主要是点、线样式），两种方式，通过importDefaults方法或者getInstance中传参：
 
    myInstance.importDefaults({  //或jsPlumb.importDefaults();
        Connector : [ "Bezier", { curviness: 150 } ],
        Anchors : [ "TopCenter", "BottomCenter" ],
        ...
    });
    //or
    var myInstance = jsPlumb.getInstance({
        Connector : [ "Bezier", { curviness: 150 } ],
        Anchors : [ "TopCenter", "BottomCenter" ],
        ...
    });

## 几个概念


