---
layout: post
title: jQuery 源码学习 (1) - $.each(), $.map(), $.proxy()
description: "想到作为一个前端没有读过 jQuery 的源码实属遗憾，决心花时间学习一下。"
tags: [jQuery, JavaScript]
---
前段时间刷掉了[《JavaScript 设计模式与开发实践》](http://book.douban.com/subject/26382780/)，是腾讯团队出品，难得的国内作者的精品技术书。想到作为一个前端没有读过 jQuery 的源码实属遗憾，决心花时间学习一下。

今天从 [src/core.js](https://github.com/jquery/jquery/blob/master/src/core.js) 开始。

## callback 函数参数顺序不一致？

首先插一句嘴：jQuery 一直让我很不解的是为什么 .each(), $.each() 和 .map(), $.map() 的 callback 函数的参数顺序是反的，明明是两个非常简单的迭代器，参数顺序应该影响不大，况且 ES5 中 [Array.prototype.forEach()](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach "forEach") 和 [Array.prototype.map()](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 两个函数的 callback 顺序是一致的。然后我发现原来 SO 上也有前辈不解，让我比较信服的是[这篇答案](http://stackoverflow.com/a/3612384/4158282)，David 兄说是因为 jQuery 是社区维护的，不同人在实现这俩功能的时候没有统一，然后等到后面发现的时候已经为时已晚，为了兼容性不能贸然去修改这两个参数的顺序，好吧。

## 概览

言归正传，在 `core.js` 中，主要是两个部分，一个是 `jQuery.fn = jQuery.prototype` 这部分内容，通过 prototype 给 jQuery 实例（如 `$('.example')` 的返回值）增加函数；另一个是 `jQuery.extend( /* something */ )`, 通过在 `core.js` 中定义的 extend 函数 (`jQuery.extend = jQuery.fn.extend = function() {}`) 为 jQuery 对象本身增加函数。把握住这两点，读懂 `core.js` 的源码应该就没有问题了。

## $.proxy()
在代码快翻到底的时候，注意！眼皮不要打架！在 `core.js` 最后出现了一个很重要的函数 `$.proxy()`！我们知道 JavaScript 函数中 `this` 称作上下文，而我们可以按照我们的心愿改变 `this` 的值，在现代的浏览器中，我们可以使用 [`Function.prototype.bind()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) (ES 5), [`Function.prototype.apply()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) (ES 3), [`Function.prototype.call()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) (JavaScript 1.3) 来改变的函数的上下文。`apply` 和 `call` 可以满足我们对浏览器兼容性的需求，而 `bind` 不兼容 IE 9 以下的浏览器；并且这两个方法虽然可以改变函数上下文，但会立即执行，但我们想要为某个函数「绑定」一个上下文而不执行的时候需要使用 `bind`, 或 jQuery 中的 `$.proxy()` 方法。

其实 `$.proxy()` 的[实现](https://github.com/jquery/jquery/blob/0c1f72667dd74bf00c6c514ebe8b7e92c3e7ad0e/src/core.js#L409-L434)和 MDN 文档中 `bind` 方法的 [Polyfill](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind#Polyfill) 相差无几。读者可以点击链接看一下源码。引起我不解的是 [425 - 428 行](https://github.com/jquery/jquery/blob/0c1f72667dd74bf00c6c514ebe8b7e92c3e7ad0e/src/core.js#L425-L428)

% highlight javascript %
args = slice.call( arguments, 2 );
proxy = function() 
  return fn.apply( context 
};
 % endhighlight % 

这里的 `slice` 是 `Array.prototype.slice` 的简写。第一次调用 `args = slice.call( arguments, 2)` 是去掉了 `$.proxy(targetFunction, thisArg, args1, args2, ...)` 中的目标函数和指定上下文，获取到函数执行的参数列表；那么为什么在后面返回的时候又将参数列表 `args` 加上 `arguments` (已被 `slice` 转为真正的数组)? 

原来，两次出现的 `arguments` 不在同样的上下文下，所指的内容也不是一样的。第一次 `arguments` 是赋值给 `$.proxy()` 的参数列表，而第二次出现的 `arguments` 则是目标函数所接收的参数列表，这两个差别请读者细细体会一下～