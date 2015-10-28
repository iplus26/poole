---
layout: post
title: 事件处理模式的不同实现之间的比较
description: "事件处理模式的不同实现之间的比较，译自 Comparison between different Observer Pattern implementations"
tags: [JavaScript, 事件]

---



译自 [Comparison between different Observer Pattern implementations](https://github.com/millermedeiros/js-signals/wiki/Comparison-between-different-Observer-Pattern-implementations)

以下的比较仅关于订阅 (subscribe) 一个事件类型、发送 (dispatch) 和移除 (remove) 一个事件监听者。我们关注的是各个概念的具体实现以及各自优劣的差异。其中列出的“缺点”可能可以通过“机智的实现”和一些 hack 来避免，但是通常情况下是存在的。

所有的实现都是完成了同样的任务，都基于同样的设计模式 ([Observer](https://zh.wikipedia.org/wiki/观察者模式)), 它们有很多的相同点只是用了稍微不同的方式。本文旨在帮你选择哪一个更适合你的工作模式和需求。

## Event Emitter / Target / Dispatcher

* 分发自定义事件的对象，需要继承自一个 EventEmitter 或 EventTarget 或 EventDispatcher 对象，或者实现合适的接口。
* 使用字符串来设置事件类型。
* DOM2/DOM3 事件基于此。

###示例代码

    {% highlight javascript %}
    myObject.addEventListener('myCustomEventTypeString', handler);
    myObject.dispatchEvent(new Event('myCustomEventTypeString'));
    myObject.removeEventListener('myCustomEventTypeString', handler);
    {% endhighlight %}

###优点

* 对于目标对象的完全控制，保证你只监听了特定目标分发的事件 (the events dispatched by the specific target). 
* 


