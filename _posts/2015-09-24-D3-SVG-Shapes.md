---
layout: post
title: D3 SVG Shapes
tags: [D3, JavaScript, visualization]

---

> 基本是对 [D3 Wiki ▸ API Reference ▸ SVG ▸ SVG Shapes](https://github.com/mbostock/d3/wiki/SVG-Shapes) 文档的全文翻译，引用的内容是我的注解，极少。
> 
> 本章节讲解 shape 的使用。从标准的 [SVG](http://www.w3schools.com/svg/) 元素开始讲起，讲到 D3 的 path data generators. 随后分别讨论了
> 
> * line generator, 基本翻完
> * area generator
> * arc generator
> * chord generator
> * diagonal generator
> * symbol generator

# SVG Shapes

SVG 有很多内置的简单形状，比如 axis-aligned rectangles 和圆形。你可以将 SVG 的 path 元素和 D3 的路径数据生成器(D3's path data generators) 一起使用，以获得更好的灵活性。

一个形状生成器(shape generator)，比如 d3.svg.arc 的返回值，既是一个对象，亦是一个函数。也就是说你可以像调用其他函数一样调用这个 shape(call the shape)，它有附加的方法可以修改自身的行为。像 D3 中的其他类一样，shapes 遵循链式的函数调用，setter 函数返回 shape 自身，允许其他的 setters 继续调用，非常简洁。

## SVG Elements

所有的 SVG shapes 可以用 transform 属性来进行变换。你可以将 transform 的效果直接应用于 shape，或是一个包含的 g 元素。因此，当一个 shape 被定义为 axis-aligned, 仅仅意味着在本地坐标系里 axis-aligned; 你仍然可以旋转或对这个 shape 进行其他操作。shapes 可以用 fill 和 stroke 样式来定义。

* svg:**rect** x="0" y="0" width="0" height="0" rx="0" ry="0"

    rect 元素定义了一个与坐标轴对齐的矩形。左上角用 *x* 和 *y* 属性来定位，用 *width* 和 *height* 确定大小。一个圆角矩形可以用可选的 *rx* 和 *ry* 属性来生成。

* svg:**circle** cx="0" cy="0" r="0"

    circle 元素定义了一个基于圆心(centre point) 和半径(radius) 的圆。圆心由 *cx* 和 *cy* 来定位，半径由 *r* 确定。

* svg:**ellipse** cx="0" cy="0" rx="0" ry="0"

    ellipse 元素定义了一个 axis-aligned 的椭圆，基于一个圆心(centre point) 和两个半径(radii, radius 的复数). 

* svg:**line** x1="0" y1="0" x2="0" y2="0"

    line 元素定义了一段线段，起始点 (x1, y1)，终点 (x2, y2). **line 元素在划线、辅助线、轴线和坐标的时候十分常用。**

* svg:**polyline** points=""
    
    polyline 元素定义了一组连接的直线段(a set of connected straight line segments). polyline 元素定义了非闭合的图形(open shapes). 构成 polyline 的点由 *points* 属性来指定。
    
    在 D3 中，使用线条生成器(d3.svg.line path generator) 和一个 path 元素，会更方便和灵活。
    
* svg:**polygon** points=""
    
    polygon 元素定义了一个由一组连接的直线段(a set of connected straight line segments) 组成的封闭的图形(closed shape). 构成 polyline 的点由 *points* 属性来指定。
    
    在 D3 中，使用线条生成器(d3.svg.line path generator) 和一个 path 元素，会更方便和灵活。线条可以使用 close path "Z" 命令来实现闭合。
    
* svg:**text** x="0" y="0" dx="0" dy="0" text-anchor="start"

    text 元素定义了一个包含文本的图形元素。text 元素的锚点由 *x* 和 *y* 来定位；另外 text 还可以使用 *dx* 和 *dy* 来实现锚点的位移。这个位移便于我们控制 text 的 margin 和基线(baseline)，可以使用 em 来控制。水平方向上的文字对齐是使用 *text-anchor* 属性来控制的。

* svg:**path** d="" transform=""

    path 元素代表了一个图形的轮廓(outline)，它可以被 filled, stroked, 用作一个 clipping path, 或是三者的任意组合。*d* 属性定义了路径数据，即路径命令的缩写(mini-language of path commands)，比如 *moveto* (M), *lineto* (L) 和 *closepath* (Z). path 元素是 SVG 中其他所有形的归纳(generalisation)，可以用来绘制几乎任何形状！

## 路径数据生成器(Path Data Generators)

为了简化 path 元素的 *d* 属性的构建流程，D3 包含了许多辅助生成路径数据(path data) 的类。每一个生成器就是一个数据的函数(Each generator is a function of data). 所以如果你的数据是一个 *xy* 坐标的序列，你可以定义 accessor 函数，路径生成器以此来生产路径数据。比方说，你可以定义一个线条生成器：

    {% highlight javascript %}
    var line = d3.svg.line()
        .x(function(d) { return d.x; })
        .y(function(d) { return d.y; })
        .interpolate("basis");
        {% endhighlight %}
        
随后，你可以用这个函数来设置 *d* 属性：

    {% highlight javascript %}
    g.append("path").attr("d", line);
    {% endhighlight %}

任何和 `g` 绑定的数据会被传给 `line` 实例。因此，这个数据必须是一个数组。对于 data 数组里面的每一个元素，*x*- 和 *y*-accessor 函数把控制点坐标(the control point coordinates) 提取出来。

一个形状生成器(shape generator)，比如 d3.svg.arc 的返回值，既是一个对象，亦是一个函数。也就是说你可以像调用其他函数一样调用这个 shape(call the shape)，它有附加的方法可以修改自身的行为。像 D3 中的其他类一样，shapes 遵循链式的函数调用，setter 函数返回 shape 自身，允许其他的 setters 继续调用，非常简洁。

d3.svg.**line**() 

构建了一个新的线条生成器(line generator)，借助于默认的 x- 和 y-accessor 函数以及线性插值(linear interpolation). 返回的函数生成路径数据(path data) 给一个开放的 piecewise linear curve, 或者说 polyline, 比方说在一个 line chart 里面：

![line chart](https://github.com/mbostock/d3/wiki/line.png)

通过修改插值，你亦可以生成 splines 和 step functions. 并且你不需担心在最后再加上路径命令(path commands). 举例来说，如果你想生成一个闭合的路径，使用一个 closepath (Z) 命令：

    {% highlight javascript %}
    g.append("path")
        .attr("d", function(d) { 
            return line(d) + "Z"; 
        });
        {% endhighlight %}

线条生成器(line generator) 是为了和区域生成器(area generator) 协同而设计的。比如，生成一个 area chart 时，你可以使用一个带 fill 样式的区域生成器和是一个带 stroke 样式的线条生成器，来着重强调区域的边缘。线条生成器只用来设置 *d* 属性，你可以用标准的 SVG 样式和属性（比如*fill*, *stroke*, *stroke-width*）来定义线条的外观。

**line**(data)

返回路径数据字符串，来自特定的 data 数组；或 null 如果路径为空。

line.**x**([*x*])

设置或返回 *x*-accessor 函数。这个 accessor 在 data 数组中的每个元素传值给线条生成器(line generator) 的时候生效。默认的 accessor 假定每个输入元素是一个 two-element array of numbers: 

    {% highlight javascript %}
    function x(d) {
        return d[0];
    }
    {% endhighlight %}
    
一般来说，使用特定的 *x*-accessor 是因为输入的数据是不同形式的，或者说你想要使用一个比例尺(scale). 比如，如果你的数据是有 `x` 和 `y` 属性的一个对象，而不是一个元组(tuple)，你应该取消引用这些属性，并且同时应用这个比例尺。

    {% highlight javascript %}
    // 使用比例尺
    var x = d3.scale.linear().range([0, w]),  
        y = d3.scale.linear().range([h, 0]);
    // 指定 x- & y-accessor
    var line = d3.svg.line()
        .x(function(d) { return x(d.x); })
        .y(function(d) { return y(d.y); });
        {% endhighlight %}

*x*-accessor 和 D3 中其他的传值函数(value functions) 生效的方式一致。函数中的 *this* 指代的是选择集中的当前元素。严格来说，*this* 同样调用了 line 函数；但是一般来说线条生成器(line generator) 传递给 attr 操作符，而 *this* 和 DOM 元素相关联。这个函数被传递了两个参数，当前的数据 datum (d) 和当前的序号 index (i). 在此，序号(index) 指的是关键点数组的序号(the index into the array of control points)，而不是当前元素在选择集中的序号。*x*-accessor 在每个数据处被使用一次，按照 data 数组指定的顺序。因此，指定一个不确定的 accessor 是可以实现的，比如一个随机数生成器。将 *x*-accessor 指定为一个常数，而不是一个函数，也是被允许的，这样一来所有点的 *x* 坐标就都一样了。

line.**y**([*y*])

其他内容都和上述 *x*- 内容相对应。值得注意的是，像其他大多数图形库一样，SVG 使用左上角作为原点，所以 *y* 值更高，在屏幕上**越低**。视觉上来说我们经常想要用左下角作为原点，为了达成这个目的，一个简单的方法是将 *y*-scale 比例尺的定义域从 [0, h] 换成 [h, 0]. 

line.**interpolate**([*interpolate*])

设置或返回插值(*interpolate*) 模式，比如

* linear - 线性的，即 polyline
* linear-closed - 闭合线条，生成一个多边形(polygon).
* step
* step-before
* step-after
* basis - B 样条(B-spline)，从[维基百科](http://en.wikipedia.org/wiki/B-spline)上看大概是这么一个东西：
![B-spline](https://upload.wikimedia.org/wikipedia/commons/thumb/1/18/B-spline_curve.svg/800px-B-spline_curve.svg.png)
* basis-open
* basis-closed
* bundle
* cardinal
* cardinal-open
* cardinal-closed
* monotone

这些插值模式的具体行为受到 tension 的影响。

如果 *interpolate* 是一个函数，那么这个函数将会把以这样形式 ([[x0, y0], [x1, y1], ...]) 表示点的数组，转换成一个 SVG 路径数据的字符串，用来显示这条线。字符串开头的 M 是暗含的，不应该被返回。举个例子，线性插值(linear interpolation) 应该按如下方式实现：

    {% highlight javascript %}
    function interpolateLinear(points) {
      return points.join("L");
    }
    {% endhighlight %}

line.**tension**([*tension*])

设置或返回 the Cardinal spline interpolation tension, 在 [0, 1] 区间内。tension 只影响 the Cardinal interpolation modes (cardinal, cardinal-open and cardinal-closed). 默认为0.7. 

注意 tension 只能够被指定为一个常数，而不可以是一个函数。但是仍然有途径能够使用同一个生成器(generator) 生成不同 tension 值的几条线，比如：

    {% highlight javascript %}
    svg.selectAll("path")
        .data([0, 0.2, 0.4, 0.6, 0.8, 1])
        .enter().append("path")
        .attr("d", function(d) { 
            return line.tension(d)(data);
        });
        {% endhighlight %}
        
在这个例子中，tension 在线条生成器每次被调用之前被设置，因此可以由相同的数据生成不同的路径。

line.**define**([*define*])

判断线条是否已定义。

> 此处省略很多翻译，详见 [line.**define**([*define*])](https://github.com/mbostock/d3/wiki/SVG-Shapes#line_defined) 原文

d3.svg.line.**radial**()

构建一个新的辐射状线条生成器(radial line generator)，借助于默认的 radius- 和 angle- 函数（假设输入的数据是一个 two-element array of numbers)，和线性插值(linear interpolation). 

返回的函数生成路径的数据，一个开放式的线性分段曲线(open piecewise linear curve)，或 polyline，正如卡迪尔线条生成器(the Cartesian line generator). 

**line**(*data*)

返回路径数据字符串给特定的 *data* 元素数组。

line.**radius**([*radius*])

设置或返回 *radius*-accessor. 

line.**angle**([*angle*])

line.**tension**([*tension*])

line.**defined**([*defined*])

d3.svg.**area**()

至此，基本将 line generator 部分翻译完了，其他部分请读者自行脑补。