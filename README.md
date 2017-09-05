# JsPlumb.js使用总结
@[元素连线|元素拖放|事件绑定]
        jsPlumb是一个强大的JavaScript连线库，提供html元素的拖放、连线等功能，可绘制不同类型、样式的连线，适用于开发web页面的图表、建模工具等。除了Dom对象，同时支持jquery对象，也可以与jquery-ui结合使用。我没有使用jquery-ui的方法来实现拖放效果，jsPlumb本身也提供相关方法，参数与jquery-ui非常类似。
        
[TOC]

## 官网
>个人觉得直接看英文官方文档比较难读，采用的学习曲线是，先搜一两篇中文资料看个大概，并结合官方文档https://jsplumbtoolkit.com/community/doc/home.html自己理解，毕竟有的翻译的会略生硬；然后看gitHub上的例子，提供了不同类型连线的demo，碰到具体的方法、参数设置等不清楚可到官网api中查看；最后可以试着开始在你的项目中使用了，这时候随时查官方api，因为你肯定需要它。
>tips： 网上的一些资料由于年代较早，我们的jsPlumb一直保持着版本更新，若引用的新版本js，有的方法已经弃用，在api中都能找到替代方法，或者到其更新日志（https://jsplumbtoolkit.com/community/doc/changelog.html ）中去查找关键字。

官方网站：https://jsplumbtoolkit.com/ 
gitHub项目地址：https://github.com/jsplumb/jsPlumb 

网络资料：
http://www.cnblogs.com/leomYili/p/6346526.html 
https://segmentfault.com/a/1190000003826470

                      

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

jsPlumb默认注册在浏览器的window对象中，为整个页面提供了一个静态实例(jsPlumb)可直接使用，你也可以用getInstance方法创建一个单独的实例。即可以直接使用全局对象[jsPlumb]或实例对象[myInstance]来调用jsPlumb的方法：

    var myInstance = jsPlumb.getInstance();

设置默认配置（主要是点、线样式），两种方式，通过importDefaults方法或者getInstance中传参。importDefaults为全局设置，可以在实例化时用getInstance重新定义，推荐使用单独的实例：
 
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

关于默认配置的详细解释如下。根据实际需要，我们一般可能只用到一部分参数。官网参考：https://jsplumbtoolkit.com/community/doc/defaults.html 。

    {
        Anchor: "BottomCenter", //锚点位置，如left，top，bottom等；对任何没有声明描点的Endpoint设置锚点，用于source及诶单或target节点
        Anchors: [ null, null ], //连线的source和target Anchor
        ConnectionsDetachable: true, //连线是否可用鼠标分离
        ConnectionOverlays: [  //连线的叠加组件，如箭头、标签
            ["Arrow", {  //箭头参数设置
                location: 1,
                visible:true,
                width:11,
                length:11,
                id:"ARROW",
                events:{
                    click:function() { }
                }
            } ],
            [ "Label", {  //标签参数设置
                location: 0.1,
                id: "label",
                cssClass: "aLabel", //hover时label的样式名
                events:{
                    tap:function() { }
                },
                visible: true
            }]
        ],
        Connector: "Bezier", //连线的类型，流程图(Flowchart)、贝塞尔曲线等
        Container: null, //父级元素id；假如页面元素所在上层不同，最外层父级一定要设置
        DoNotThrowErrors: false, //如果请求不存在的Anchor、Endpoint或Connector，是否抛异常
        DragOptions: {cursor: 'pointer', zIndex: 2000}, //通过jsPlumb.draggable拖拽元素时的默认参数设置
        DropOptions: { }, //target Endpoint放置时的默认参数设置
        Endpoint: "Dot", //端点（锚点）的样式声明
        Endpoints: [ null, null ], //用jsPlumb.connect创建连接时，source端点和target端点的样式设置
        EndpointOverlays: [ ], //端点的叠加物
        EndpointStyle: { fill : "#456" }, //端点的默认样式
        EndpointStyles: [ null, null ], //连线的source和target端点的样式
        EndpointHoverStyle: { fill: "#ec9f2e" }, //端点hover时的样式
        EndpointHoverStyles: [ null, null ], //连线的source和target端点hover时的样式
        HoverPaintStyle: {stroke: "#ec9f2e" }, //连线hover时的样式
        LabelStyle: { color: "black" }, //标签的默认样式，用css写法。
        LogEnabled: false, //是否开启jsPlumb内部日志
        Overlays: [ ], //连线和端点的叠加物
        MaxConnections: 1, //端点支持的最大连接数
        PaintStyle: { lineWidth : 8, stroke : "#456" }, //连线样式
        ReattachConnections: false, //是否重新连接使用鼠标分离的线?
        RenderMode: "svg", //默认渲染模式
        Scope: "jsPlumb_DefaultScope" //范围，具有相同scope的点才可连接？
    }

点或线的一些参数设置，在上述全局默认配置中也并不是必须，因为在使用connect方法创建连接和addEndpoint 方法添加锚点时都可以对其进行重写。

下面看个实际的例子：
    

## 几个概念
详见文档：https://jsplumbtoolkit.com/community/doc/basic-concepts.html 
####Anchor
锚点位置，EndPoint所处的位置。
锚点位置可以是固定的(Static)、动态(Dynamic)、周边锚(Perimeter anchors)、连续锚(Continuous anchors)四种类型。
####Endpoint
端点，连线末端可见的端点。
创建Endpoint可以通过多种方式：
1. 通过jsPlumb.connect(...)创建连接时动态创建，传入参数为source和target元素id或DOM元素；        
        
       
    myInstance.connect({uuids: ["sourceId", "targetId" ], overlays: [...],  detachable: true,  reattach: true, editable: true});
    
2. 通过jsPlumb.addEndpoint(...)创建Endpoint添加到元素上
        
   
    //Endpoint的配置参数，尽可能多的列出了以下这些：
    //需要区别的是，此处是针对Endpoint点的定义设置，在全局配置中也有一些同名的属性，在那里是针对线的样式设置。
    var endpoint = {
         endpoint: "Dot",  //四种类型Dot，Rectangle，Image，Blank 
         anchor: [0.5, 0.5, 0, -1],  //锚点位置
         paintStyle: { fill: "#7AB02C", radius: 5 },  //点的样式
         hoverPaintStyle: { fill: "#216477", stroke: "#216477" }, //点hover时的样式
         connector: [ "Flowchart", { stub: [40, 60], gap: 10, cornerRadius: 5, alwaysRespectStubs: true, midpoint: 0.3 } ],  //连线设置
         connectorStyle: { strokeWidth: 7, stroke: "rgba(198,89,30,0.7)" },  //连线样式
         connectorHoverStyle: {},  //连线hover时样式
         endpointsOnTop: true,
         isSource: true,  //是否为源端点
         maxConnections: 10,  //最大连接数，若为-1则不限制连接数
         isTarget: true,  //是否为目标端点
         dragOptions: {}
         dropOptions: { tolerance: "touch", hoverClass: "className" }, //从source拖出时 target点的样式
         overlays: [
             [ "Label", {
                    location: [0.5, 1.5],
                    label: "Drag",
                    cssClass: "className", 
                    visible: false
                } 
             ]    
    };
    var e1 = instance.addEndpoint( "elId1", endpoint );
    var e2 = instance.addEndpoint( "elId2", endpoint );
    myInstance.connect({ source: e1, target: e2 });
    
3. 当对元素设置过jsPlumb.makeSource(...)时，从元素拖拽出连线时就会创建并分配Endpoint。

    myInstance.makeSource(el, endpoint); //el为元素对象，endpoint为上述配置对象
    
####Connector
连线。两个页面元素直接的连线，有四种类型，贝塞尔曲线(Bezier)、直线(Straight)、流程图(Flowchart)、状态器(StateMachine)，其中后两种类型支持连线的首末端都在一个元素上，形成回路。
https://jsplumbtoolkit.com/community/doc/connectors.html
使用connect方法创建连线：

    myInstance.connect(...);

####Overlay
叠加。连线上的装饰的组件，例如箭头(Arrow)、标签(Label)等等。具体参数前面的例子中有，详见官网。
https://jsplumbtoolkit.com/community/doc/overlays.html 
在前面的默认配置和Endpoint参数配置中已有相关的例子。
####Group
分组


