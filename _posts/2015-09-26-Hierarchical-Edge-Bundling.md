---
layout: post
title: 学习 Hierarchical Edge Bundling 小记
description: "我需要实现一个 Hierarchical Edge Bundling 的关系图，这是学习的一部分笔记"
tags: [D3, visualization 可视化, JavaScript]

---

本来已经困得不行了，洗了个澡又清醒一点。十点钟去睡觉似乎有点早（刚刚九点钟的时候想去睡觉就可以叫做 dramatic），再说还有做不完的事情等着我（奋斗脸）。

实习的公司 boss 叫我想法儿实现一个 [Hierarchical Edge Bundling](http://bl.ocks.org/mbostock/1044242), 这里还有一个[高亮线条的版本](http://bl.ocks.org/mbostock/7607999)。

{% gist 1044242 readme.md %}

这两天看了 [SVG Shapes](http://ivanjiang.com/blog/2015/09/24/D3-SVG-Shapes/) 和 [Ordinal Scales](http://ivanjiang.com/blog/2015/09/26/d3-ordinal-scales/) 的文档，然后翻译了一下。看英文文档对我来说是一件非常枯燥而漫长的事情，主要还是语言上的障碍，自然是没有母语看起来顺畅的，有些句子不参考代码或图示还是不明白是什么意思。但是这样做的好处是，一来看英文文档能够让我对这个东西的理解更深入，避免拾人牙慧，英语非母语而学编程的一个缺陷在于，有很多英语本身的暗示你得不到，比如 var 是 variable 的缩写，p 标签是 paragraph 的缩写，这样理解有可能会有偏差，看原文某种程度上弥补一下；二来通过翻译这件事儿，我能够很专注，不会漏掉一个句子，顶多翻不完，但是翻过的印象还是比较深刻的。

而  [Hierarchical Edge Bundling](http://bl.ocks.org/mbostock/1044242) 这个关系图里面，主要用到了

* [集群(Cluster Layout)](https://github.com/mbostock/d3/wiki/Cluster-Layout), 实际上是[层级布局(Hierarchy Layout)](https://github.com/mbostock/d3/wiki/Hierarchy-Layout) 的一个实现(implement). 
* [Bundle Layout](https://github.com/mbostock/d3/wiki/Bundle-Layout)
* Line, 这个是我之前翻的[那篇](http://ivanjiang.com/blog/2015/09/24/D3-SVG-Shapes/)的部分
* [d3.json](https://github.com/mbostock/d3/wiki/Requests#d3_json), 用来获取外部的 json 数据作图

因为我对数学很头大，所以我们挑一些简单的关系来看一下这个图到底是什么意思：

{% highlight json %}
    {"name":"flare.physics.DragForce","size":1082,"imports":["flare.physics.Simulation","flare.physics.Particle","flare.physics.IForce"]},
{% endhighlight %}

{% highlight json %}
    {"name":"flare.physics.Simulation","size":9983,"imports":["flare.physics.Particle","flare.physics.NBodyForce","flare.physics.DragForce","flare.physics.GravityForce","flare.physics.Spring","flare.physics.SpringForce","flare.physics.IForce"]},
{% endhighlight %}

{% highlight json %}
    {"name":"flare.vis.operator.layout.ForceDirectedLayout","size":8411,"imports":["flare.physics.Simulation","flare.animate.Transitioner","flare.vis.data.NodeSprite","flare.vis.data.DataSprite","flare.physics.Particle","flare.physics.Spring","flare.vis.operator.layout.Layout","flare.vis.data.EdgeSprite","flare.vis.data.Data"]},
{% endhighlight %}

这幅图就是一个软件依赖图。具体来说，`DragForce` 是属于 `physics` 这个包里的；如果你想要使用 `physics.DragForce`包，因为它依赖 `Simulation`, `Particle`, `IForce` 这三个包，所以需要 import 它们。所以 **import** 关系是一个单向链，不会存在循环。

## Bundle Layout

这是对 Danny Holten 的 [hierarchical edge bundling](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.220.8113&rep=rep1&type=pdf) 算法的实现。对于每个输入的链接，都会生成一条路径，连接最仅共同祖先(the least common ancestor) 的父级(the parent hierarchy), 和目标节点。这一系列的节点可以和其他的层级布局(hierarchical layout)](https://github.com/mbostock/d3/wiki/Hierarchy-Layout) 一起使用，比如集群(cluster), 以在节点之间生成 bundled splines. 

![](https://github.com/mbostock/d3/wiki/bundle.png)

举个例子，你可以参考[软件依赖(software dependencies)](http://bl.ocks.org/mbostock/1044242) 的可视化。

d3.layout.**bundle**()

构建一个默认的 bundle 布局。当前 bundle 布局是 stateless 的，所以只有一个默认的设置。返回的布局对象(layout object) 既是一个对象也是一个函数，这意味着你可以像调用其他任何函数一样调用它，它亦有其他附加的方法可以改变它的行为。像 D3 中的其他类一样，布局遵循链式的函数调用并返回它本身，允许其他 setters 接着使用，这非常简洁。

**bundle**(*links*)

根据指定的 *links* 数组来估算 bundle 布局，返回计算好的、从源到目标的路径，经过最近共同祖先(the least common ancestor). 输入的每一条连接(link) 必须要有个属性：

* source - 源节点
* target - 目标节点

并且每个节点必须要有一个属性：

* parent - 父节点

这是 [hierarchy layouts](https://github.com/mbostock/d3/wiki/Hierarchy-Layout) 生成的一个子集，返回值是一个路径的数组，每一条路径都是由节点的数组表示的。因此，bundle 布局并不直接计算基本的线条(the basis splines); 而是返回一个节点数组，隐含地表示线条的控制点(the control points). 你可以将这个数组和 d3.svg.line 或者 d3.svg.line.radial 联合起来使用，以生成线条(splines). 举个例子，如果你想要使用一个 cluster: 

{% highlight javascript %}
var cluster = d3.layout.cluster()
    .size([2 * Math.PI, 500]);
    {% endhighlight %}
    
一个适合 hierarchical edge bundling 的线条生成器(line generator) 将会是这样的：

{% highlight javascript %}
var line = d3.svg.line.radial()
    .interpolate("bundle")
    .tension(.85)
    .radius(function(d) { return d.y; })
    .angle(function(d) { return d.x; });
{% endhighlight %}

bundle 布局为和**线条生成器的 bundle 插值模式**(the line generator's "bundle" interpolation mode) 协作而生，虽然严格来讲你可以使用任何插值或形状生成器(interpolator or shape generator). Holten 的 bundle 的 strength 参数是由线条(line) 的 tension 决定的。

## Cluster Layout 

集群布局(cluster layout) 生产 dendrograms: 节点连接的图，相同深度的叶节点。举个例子，一个集群布局可以用来组织一个包结构里面的软件类（就像我们一直在讨论的那个图一样）：

![](https://github.com/mbostock/d3/wiki/cluster.png)

像 D3 中的其他类一样，布局遵循链式的方法调用。

d3.layout.**cluster**()

创建一个新的集群布局，设定默认：默认的排序(sort order) 是 null, 默认的 children accessor 假定每一个输入的数据都是一个有子数组的对象，默认的 separation 函数对姊妹节点(siblings) 用一个节点宽度，非姊妹节点(non-siblings) 用两个节点宽度，默认的 size 是 1×1.

**cluster**(*root*)

cluster.**nodes**(*root*)

返回 *root* 节点相关联的节点数组。集群布局(cluster layout) 是 D3 hierarchical 布局族的一部分。这些布局遵循一个基本的结构：输入的参数是这个集团(hierarchy) 的 *root* 节点，输出的是一个数组包含所有节点的计算后的位置。每个节点都有几个属性：

* parent - 父节点，如果此节点是根节点的话则为 null
* children - 子节点的数组，如果此节点是叶节点的话则为 null
* depth - 节点深度，从根节点为 0 开始
* x - 计算所得的节点位置的 x 坐标
* y - 计算所得的节点位置的 y 坐标

你不仅可以将 *x* 和 *y* 用于横纵坐标，亦可以用作半径(radius) 和角度(angle) 以制作一个辐射状的图，而不是一个 Cartesian 布局。

cluster.**links**(*nodes*)

给出一个 *nodes* 数组，比如 nodes() 函数返回的，返回一个对象数组，表示每个节点的父节点到子节点的连接。叶结点没有任何连接。每一个连接是一个有两个属性的对象：

* source - 父节点
* target - 子节点（当前节点）

This method is useful for retrieving a set of link descriptions suitable for display, 常和 diagonal shape generator 一起使用。比如：

{% highlight javascript %}
svg.selectAll("path")
    .data(cluster.links(nodes))  // 
    .enter().append("path")
    .attr("d", d3.svg.diagonal());
    {% endhighlight %}


cluster.**children**([*children*])

cluster.**sort**([*comparator*])

cluster.**separation**([*separation*])

cluster.**size**([*size*])

如果 *size* 指定了，将布局的尺寸设置为指定的两个数的数组，分别代表 *x* 和 *y*. 如果 *size* 没有指定，则返回当前的尺寸，默认为 1×1，或为 null 如果 nodeSize 正在被使用。虽然有 *x* 和 *y* 来表示布局的大小，但这可以表示任意一个坐标系。比如说，想要制作一个径向布局(radial layout), 树的广度(*x*) 是用度(degree) 来计算的，树的深度(*y*) 是用半径 *r* 像素来计算的，就用 [360, *r*]. 

cluster.**nodeSize**([*nodeSize*])

如果 *nodeSize* 被指定了，将每个节点的大小设置为数组中的两个数，分别为 *x* 和 *y*. 如果 *nodeSize* 没有指定，则返回当前节点的大小，默认为 null, 意味着 the layout has an overall fixed size, which can be retrieved using size. 

cluster.**value**([*value*])

如果 *value* 被指定，将 value accessor 设置为特定的函数。如果 *value* 未被指定，返回当前的 value accessor, 默认为 null, 意味着 value 属性未被计算。如果被指定，value accessor 会被每一个输入的数据元素调用，必须返回一个数表示节点的数值。这个值对集群布局(cluster layout) 没有任何影响，只是 hierarchy layouts 提供的一个通用的功能。

## 再看代码

让我们回头看一下想要实现的那个软件依赖图里面的部分相关代码：

{% highlight javascript %}
var diameter = 960,
    radius = diameter / 2,
    innerRadius = radius - 120;
    
var cluster = d3.layout.cluster()
    .size([360, innerRadius])
    .sort(null)
    .value(function(d) { return d.size; });
// 生成一个 cluster 布局，极坐标系，半径是 innerRadius = 360

var bundle = d3.layout.bundle();  
// 下面用到 bundle(links) 这种用法

var line = d3.svg.line.radial()
    .interpolate("bundle")
    .tension(.85)
    .radius(function(d) { return d.y; })
    .angle(function(d) { return d.x / 180 * Math.PI; });
{% endhighlight %}

首先是生成了一个集群(cluster) 布局，size 是

用线条生成器生成一个辐射状的(radial) 的



