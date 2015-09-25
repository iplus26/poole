---
layout: post
category: gh-pages
tag: jekyll, poole, disqus, github pages
title: 使用 Jekyll 和 Poole 快速搭建 blog 以及评论
---

因为昨天受累翻了[一篇 D3 的文档]({% post_url 2015-09-24-d3-svg-shapes %})，加上之前有[一篇关于 MongoDB 的短文翻译]({% post_url 2015-09-18-mongodb-cursor-explain-method %})，所以决定给自己弄个博客。之前因为嫌弃 hexo 限制太多了，想着干脆自己从头到尾搭一个博客算了，但是因为<del>一直腾不出时间</del>懒，又烂尾了。今天一早起来发现 [Poole](https://github.com/poole/poole)，于是就赶紧自己搭了一下。把遇到的坑罗列入下：

## 安装

[Poole 的安装](https://github.com/poole/poole#usage)很简单（对于 Linux/Mac 来说，Windows 详见官网教程），首先在命令行中输入：

    $ gem install jekyll
 
安装 Jekyll gem. 因为我对此几乎一窍不通，所以希望跟着教程不会出错，然后就出错了：
 
    ERROR: Error installing jekyll:
    ERROR: Failed to build gem native extension. 

解决方案大多是 linux 下的；在 [Stack Overflow](http://stackoverflow.com/questions/8389301/os-x-rails-failed-to-build-gem-native-extension) 找到了答案，是因为 Mac 是不能编译任何 native ruby extension 的，除非安装 Apple developer tools (the Command Line tools). 安装方法如下：

    $ xcode-select --install
    
接着会弹出来一个对话框，提示安装 the Command Line tools. 

安装完后即可顺利安装。

## 加入 Disqus 评论框

这个博客托管于 GitHub Pages，仅支持静态页面，Jekyll 的原理就是用服务器将我们的文章(posts) 加上 header, sidebar 等等编译成静态页面，在 `_site` 文件夹下。因此如果需要加上评论功能的话，我们只能借助于第三方插件，比如国外十分流行的 [Disqus](https://disqus.com) 评论框，国内有多说等，对国内的社交网络支持更好。

Disqus 对 Jekyll 十分友好，有[官方教程](https://help.disqus.com/customer/portal/articles/472138-jekyll-installation-instructions)指导。我还参考了 [Ankur Gupta](http://www.perfectlyrandom.org/about/) 的这篇 [Adding Disqus to your Jekyll](http://www.perfectlyrandom.org/2014/06/29/adding-disqus-to-your-jekyll-powered-github-pages/), 很详尽。

## 文章地址有两个 // ?

很快搭建好了 blog 之后，我发现我的文章地址是长成这样的

    http://ivanjiang.com/blog//2015/09/25/build-blog-with-poole/
    
有两个 `/` 是什么鬼！Google 了半天，[大家都说原因在于 baseurl 的误用](https://github.com/jekyll/jekyll-feed/issues/63)，然而我的配置应该是没有出问题的：

    url:       http://ivanjiang.com
    baseurl:   "/blog"

如果我把 `/blog` 换成 `/blog/` 就会出现三个 `/`...

后来在 Jekyll 的官方文档中找到了[答案](http://jekyllrb.com/docs/permalinks/#template-variables)。因为我采用了 [*pretty*](http://jekyllrb.com/docs/permalinks/#built-in-permalink-styles) 的链接风格(permalink style)，因此我的链接格式为：

    /:categories/:year/:month/:day/:title/
    
然而我没有对文章设置分类(category)，所以会自动输出两个斜杠。

>The specified categories for this Post. If a post has multiple categories, Jekyll will create a hierarchy (e.g. /category1/category2). Also Jekyll automatically parses out **double slashes** in the URLs, so if no categories are present, it will ignore this.

### 参考

[Jekeyll Documentation](http://jekyllrb.com/docs/home/)