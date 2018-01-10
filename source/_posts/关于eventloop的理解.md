---
title: 关于eventloop的理解
date: 2018-01-03 20:00:36
categories: [JavaScript]
tags: [JavaScript, event loop]
---
#### 唠唠叨叨
距离上一篇博客已经快一年，制定2018年的目标的时候，想了想2017年的基本没有完成，就把2017改个title，变成2018的然后就完事。在此希望18年不会再如此荒废。闲话不多说，进入正题。
#### 写在前面
众所周知，JavaScript是单线程的，任务都只在一个线程上面执行，那么意味着只有等前面一个任务处理完毕，才会执行后一个任务，如果前一个任务执行时间很长，后面的任务就只能一直等着，那么对于ajax、Dom操作，setTimeout、Promise等这些耗时的任务，JavaScript是怎么处理的（不同环境不同实现，这里应该是相应的JavaScript运行环境是怎么处理的），以不至于让程序处于一直等待的状态。event loop就是JavaScript用来解决上述问题的一种机制。
#### 事件循环（event loop）
先讲讲什么是事件循环，
w3c标准给的原文：To coordinate events, user interaction, scripts, rendering, networking, and so forth, user agents must use event loops as described in this section. There are two kinds of event loops: those for browsing contexts, and those for workers.（为了协调事件，用户交互，脚本，渲染，网络等，用户代理必须使用本节描述的事件循环。有两种事件循环：用于浏览上下文的事件循环，以及针对workers的事件循环）。这个解释跟上述为什么要有event loop相吻合。
事件循环是怎么做的：Javascript在主线程运行的时候，会有一个堆，一个执行栈，一个事件循环，一个或者多个任务队列。堆：相关的对象会被分配在堆中。在Javascript运行时，程序中的代码会依次进入执行栈中，然后执行，当遇到需要执行时间较长任务（或者说需要异步的任务，使浏览器不至于假死）时，如setTimeout，ajax，Dom操作等，Javascript就会把这些任务交给浏览器的其他模块处理，使其不至于阻塞主线程的执行，等浏览器的模块处理完这些任务后，会把相应的回调压入任务队列中，等执行栈中的事件执行完毕后，主线程通过事件循环去任务队列中读取相应的回调，并把回调加入执行栈执行。等执行栈的任务执行完之后，再去任务队列读取相应回调，如此往复。如下图：
<img src="/images/eventloop/eventloop1.jpeg" style="width: 400px;">
下面用人家演讲的视频截图来说明这个过程。

程序等待执行时，执行栈，webAPI，任务队列都为空。
<img src="/images/eventloop/eventloop2.jpeg" style="width: 600px;">

程序开始执行时，先将一个main()主方法加入执行栈中，此方法相当于程序的入口。
<img src="/images/eventloop/eventloop3.jpeg" style="width: 600px;">

继续执行console.log('Hi')方法，方法加入执行栈中；然后立即执行并移出执行栈。输出Hi
<img src="/images/eventloop/eventloop4.jpeg" style="width: 600px;">
<img src="/images/eventloop/eventloop5.jpeg" style="width: 600px;">

程序继续往下执行，将setTimeout加入执行栈中并执行，浏览器通过相应的模块创建一个计时器，来处理倒计时的任务，然后setTimeout移出执行栈。
<img src="/images/eventloop/eventloop6.jpeg" style="width: 600px;">
<img src="/images/eventloop/eventloop7.jpeg" style="width: 600px;">
<img src="/images/eventloop/eventloop8.jpeg" style="width: 600px;">

在setTimeout方法出栈执行后，立即将下面的console.log加入执行栈中，然后立即执行并移出执行栈。输出SJS。
<img src="/images/eventloop/eventloop9.jpeg" style="width: 600px;">
<img src="/images/eventloop/eventloop10.jpeg" style="width: 600px;">

主函数main()执行完毕，移出执行栈。
<img src="/images/eventloop/eventloop11.jpeg" style="width: 600px;">

等计时器达到指定的条件后，就是延时5s后，会将回调方法压入任务队列中，也就是下面的task queue中。
<img src="/images/eventloop/eventloop12.jpeg" style="width: 600px;">

当执行栈为空时，系统就通过事件循环去轮询任务队列，查看是否有任务需要执行，这时候就检查到了达到条件的回调方法，然后事件循环就把检查到的方法加入到执行栈中，并依次调用回调里面的方法，然后依次执行方法，运行console.log，输出there，都执行完毕，清空执行栈。事件循环继续去轮询任务队列，并如此反复。
<img src="/images/eventloop/eventloop13.jpeg" style="width: 600px;">
<img src="/images/eventloop/eventloop14.jpeg" style="width: 600px;">
<img src="/images/eventloop/eventloop15.jpeg" style="width: 600px;">
<img src="/images/eventloop/eventloop16.jpeg" style="width: 600px;">
<img src="/images/eventloop/eventloop17.jpeg" style="width: 600px;">

#### 总结
上述只是通过setTimeout大概讲述了下事件循环的过程，下面会以这个为基础来讲讲setTimeout、setInterval、Dom Event、Promise等是如何工作的，执行的先后顺序又是怎么样，以加深对整个事件循环的理解。
每个环境中会包含的一个或多个任务队列，不同的环境事件循环读取每个队列的先后顺序有可能是不一样的，但大体大同小异。顺带提一下web worker有自己的堆、栈、消息队列和事件循环，两个不同的运行时只能通过postMessage方法进行通信。然后Node环境的实现又是不一样(Node的要等做完功课后再来介绍)，当然这篇文章主要是看各位大神的文章拼凑而成的，只加上一点点自己的小理解，自己写一遍加深记忆，也权当做个笔记。
恩~ 以上如有错误遗漏，望更正补充。
#### 参考资料
[W3: Event loops](https://www.w3.org/TR/html5/webappapis.html#event-loops)
[MDN：并发模型与事件循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop)
[事件循环示例，可在线写代码，观看过程](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gZ2V0WSAoeCkgewogICAgcmV0dXJuIG5ldyBQcm9taXNlKGZ1bmN0aW9uIHByb21pc2UocmVzb2x2ZSwgcmVqZWN0KSB7CiAgICAgICAgc2V0VGltZW91dChmdW5jdGlvbiBwcm9taXNlVGltZW91dCgpIHsKICAgICAgICAgICAgcmVzb2x2ZSgoMyAqIHgpIC0gMSk7CiAgICAgICAgfSwgMCk7CiAgICB9KTsKfQoKZnVuY3Rpb24gZm9vIChiYXIsIGJheikgewogICAgdmFyIHggPSBiYXIgKiBiYXo7CgogICAgcmV0dXJuIGdldFkoeCkKICAgICAgICAudGhlbihmdW5jdGlvbiByZXR1cm5BcnJheSh5KSB7CiAgICAgICAgICAgIHJldHVybiBbIHgsIHkgXTsKICAgICAgICB9KTsKfQpzZXRUaW1lb3V0KGZ1bmN0aW9uIHRpbWVvdXQoKSB7CiAgICBjb25zb2xlLmxvZygndGltZW91dCcpCn0sIDApCgpmb28oMTAsIDIwKS50aGVuKGZ1bmN0aW9uIGxvZ01zZyhtc2dzKSB7CiAgICBjb25zb2xlLmxvZyhtc2cpOwp9KTs%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)
[【转向Javascript系列】从setTimeout说事件循环模型](http://www.alloyteam.com/2015/10/turning-to-javascript-series-from-settimeout-said-the-event-loop-model/)
[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
[【朴灵评注】JavaScript 运行机制详解：再谈Event Loop](http://blog.csdn.net/lin_credible/article/details/40143961)
[How JavaScript works: Event loop and the rise of Async programming + 5 ways to better coding with async/await](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5)
[Philip Roberts: Help, I’m stuck in an event-loop.](https://vimeo.com/96425312)
