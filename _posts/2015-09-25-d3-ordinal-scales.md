---
layout: post
tag: d3
title: D3 Ordinal Scales
---

# Ordinal Scales

Scales（我译为比例尺）是将输入的定义域映射(map) 为一个输出范围。Ordinal scales 有一个不连续的定义域，比如一组名字或者类别。有连续定义域（比如说一组实数）的比例尺被称为 quantitative scales. Scales 是 D3 中可选的特性(an optional feature), 你不一定要使用它们，如果你想自己做计算的话。但是使用比例尺可以极大地简化将一维数映射到一个可视化表示的代码量。

一个 scale 对象，比如 d3.scale.ordinal 返回的，既是一个对象亦是一个函数。这意味着，你可以像调用其他函数一样调用它，并且它也有其他函数来影响它的行为。像 D3 中的其他类一样，scales 遵循函数的链式调用，setter 函数将会返回 scale 自身，并且允许多个 setters 的调用，这非常简洁。

>上一段在很多地方反复出现过了，正因为多数类都支持这一特性，使得 D3 可以实现链式调用。

d3.scale.**ordinal**()

构造了一个新的 ordinal scale（序数比例尺，我不知道怎么准确翻译它，正如上面所说，这是一个离散的定义域），定义域和值域均为空。只有输出范围（值域）确定了，这个 ordinal scale 才有效，否则一直返回 undefined.

**ordinal**(*x*)

在定义域中给出一个 *x* 值，返回值域(output range) 中对应的值。

如果值域被 range 函数（而不是 rangeBands, rangeRoundBands 或 rangPoints）明确地指定了，并且给出的 *x* 不是在比例尺的定义域中，那么 *x* 将会被默认加入到定义域中；随后再给出这个 *x* 值时，scale 会返回对应的 *y* 值。

ordinal.**domain**([*values*])

如果 *values* 被指定了，将这个数组设置为 ordinal scale 的定义域。*values* 的第一个元素会被映射为值域的第一个元素，第二个元素会被映射为值域的第二个元素，以此类推。定义域的值会被内部存储为一个关联数组(an associative array), 将值和序号(index) 对应起来；这个序号会用来接收从值域传过来的值。因此，一个 ordinal scale 的值一定能够被强制转换为一个 string 类型，这样被转换后的定义域的值和对应值域的值一一对应。如果 *values* 没有被指定，这个函数会返回当前的定义域。

设置 ordinal scale 的定义域是可选的；如果定义域没有被设定，那么值域一定要被设置。那么，每一个传递给 scale 函数的值，会被设置为一个从值域传过来的新值；换句话说，定义域会根据实际使用情况来决定。虽然定义域可以被隐含地构建，但更明智的做法是显式地设定它，以确保一切尽在掌握，因为根据使用情况来隐式设置，会依赖于顺序(as inferring the domain from usage will be dependent on ordering).

ordinal.**range**([*values*])

如果 *values* 被指定了，将这个数组设置为 ordinal scale 的值域。*values* 的第一个元素会被映射为定义域的第一个元素，第二个元素会被映射为定义域的第二个元素，以此类推。如果值域中的元素少于定义域中的元素，scale 将会从值域的第一个元素开始循环。如果 *values* 未指定，那么这个函数将会返回当前的值域。

这个函数是为明确计算离散的输出值时准备的，比如一组可分类的颜色值。在其他情况下，比如计算散布图(ordinal scatterplot) 或是柱状图(bar chart) 的布局的时候，你可以使用 rangePoints 或者 rangeBands 更方便一些。

ordinal.**rangePoints**(*interval*[, *padding*])

由特定的连续间隔(continuous interval) 设置输出的值域。*interval* 数组包含了两个元素，分别是最小值和最大值。这段间隔会被平均分配为 *n* 个等距的点(**points**)，*n* 是输入定义域内（独立的）值的数量。第一个和最后一个点会根据指定的 *padding* 值和边缘产生一个位移，*padding* 值默认为 0. *padding* 值将会用作点间距的倍数；一个合理的取值是 1.0，这样第一个和最后一个点与最小值和最大值之间的位移就是点间距的一半。

![](https://camo.githubusercontent.com/1f2b6fd134f82ce192002ec3944eccb09c748abe/68747470733a2f2f662e636c6f75642e6769746875622e636f6d2f6173736574732f3233303534312f3533383638392f34366438373131382d633139332d313165322d383361622d3230303864663763333661612e706e67)

    {% highlight javascript %}
    var o = d3.scale.ordinal()
        .domain([1, 2, 3, 4])
        .rangePoints([0, 100]);
    
    o.range(); // [0, 33.333333333333336, 66.66666666666667, 100]
    {% endhighlight %}

ordinal.**rangeRoundPoints**(*interval*[, *padding*])

和 rangePoints 类似，另外它还确保值域的值是整数。

    {% highlight javascript %}
    var o = d3.scale.ordinal()
        .domain([1, 2, 3, 4])
        .rangeRoundPoints([0, 100]);
    
    o.range(); // [1, 34, 67, 100]
    {% endhighlight %}

注意取整操作(rounding) 引入了额外的外边距(outer padding), 平均而言，和定义域的长度成比例。比方说，一个定义域的长度是 50，则每边需要额外 25px 长度的外边距。值域的长度越接近于定义域长度的倍数，会有效地减少额外需要的边距。

    {% highlight javascript %}
    var o = d3.scale.ordinal()
        .domain(d3.range(50))
        .rangeRoundPoints([0, 95]);

    o.range(); // [23, 24, 25, …, 70, 71, 72]
    o.rangeRoundPoints([0, 100]);
    o.range(); // [1, 3, 5, …, 95, 97, 98] 
    {% endhighlight %}
    
（或者你可以手动修改 scale 的输出，或者使用一个 shape-rendering: crispEdges. 但是，这会导致不规则分布的点。）

ordinal.**rangeBands**(*interval*[, *padding*[, *outerPadding*]])

由特定的连续间隔(continuous interval) 设置输出的值域。*interval* 数组包含了两个元素，分别是最小值和最大值。这个间隔会被分割成 *n* 个平均分的段(**bands**)，而 *n* 是定义域中（独立的）值的数目。*padding* 值设置了每段之间的间隔，默认为 0；段和边界的位移(offset) 也可能受其影响。边距(padding) 在 [0,1] 的范围内，并且和值域空间有关，决定分配多少给边距(The padding is typically in the range [0,1] and corresponds to the amount of space in the range interval to allocate to padding). 如果值为 0.5，分段(band) 的宽度和边距宽度(the padding width) 相等。*outerpadding* 参数是对于整个分段的组合来说的；值为 0 的话，就没有这个内边距了。

![](https://camo.githubusercontent.com/12675eaff20815f41bccd4d1c50643c2b531052e/68747470733a2f2f662e636c6f75642e6769746875622e636f6d2f6173736574732f3233303534312f3533383638382f34366332393863302d633139332d313165322d396137652d3135643961626366616239622e706e67)

    {% highlight javascript %}
    var o = d3.scale.ordinal()
        .domain([1, 2, 3])
        .rangeBands([0, 100]);

    o.rangeBand(); // 33.333333333333336
    o.range(); // [0, 33.333333333333336, 66.66666666666667]
    o.rangeExtent(); // [0, 100]
    {% endhighlight %}

>注意上述代码中的 rangeBand() 方法和上述提到的 rangeBands() 方法的区别，rangeBand() 用于返回当前的分段的长度。rangeBand() 和 rangeExtent() 方法将在下面讲到。