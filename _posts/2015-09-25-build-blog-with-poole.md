---
layout: post
tag: jekyll, poole, disqus, github pages
title: 使用 Jekyll 和 Poole 快速搭建 blog 以及评论
---
    
<del>然而我没有对文章设置分类(category)，所以会自动输出两个斜杠。</del>

<del>The specified categories for this Post. If a post has multiple categories, Jekyll will create a hierarchy (e.g. /category1/category2). Also Jekyll automatically parses out **double slashes** in the URLs, so if no categories are present, it will ignore this.</del>

这显然是 Jekyll 在编译的时候出现了一些问题，我发现这其实是 GitHub Pages 的原因，使用 GH 的 Jekyll 服务器有一些[不同](https://jekyllrb.com/docs/github-pages/)。

>When doing permalinks or internal links, do it like this: `{{ post.url }} `– note that there is no slash between the two variables.

所以在我的 `index.html` 中，将

    {% highlight html %}
    <a href="{{ post.url }}">
    {% endhighlight %}
    
改为

    {% highlight html %}
    <a href="{{ post.url }}">
    {% endhighlight %}

即可影响 Jekyll 的编译。

### 参考

[Jekeyll Documentation](http://jekyllrb.com/docs/home/)