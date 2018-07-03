---
layout: post
title: JavaScript 的运行机制
tags: [Front-End]
---

**摘要：**众所周知，JavaScript 这门语言的核心特征就是单线程，也就是在 JS 引擎中负责解释和执行 JavaScript 代码的线程只有一个。JavaScript 的单线程与它的用途有关。作为浏览器脚本语言，JavaScript 的主要用途是与用户互动以及操作 DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，JavaScript 同时有两个线程，一个线程在某个 DOM 节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？所以，为了避免复杂性，从一诞生 JavaScript 就是单线程，这已经成了这门语言的核心特征，将来也不会改变。尽管 HTML5 提出了 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，而且不得操作 DOM。所以，这个新标准并没有改变 JavaScript 单线程的本质。
{:.message}

单线程也就意味着所有任务都需要排队，前一个任务结束后才会执行后一个任务。如果前一个任务耗时很长，后一个任务就不得不一直等着。为了避免这种需要长时间等待的同步阻塞，所有任务又可以分为两种：同步任务和异步任务。显然同步任务就是指在主线程上排队执行的任务。而异步任务指的是，不进入主线程、而进入消息队列的任务，只有消息队列通知主线程某个异步任务可以执行了，该任务才会进入主线程执行。所以在主线程之外，还存在一个消息队列。只要异步任务有了运行结果，就在消息队列之中放置一个消息，表示相关的异步任务可以执行了。只要主线程空了，就会去读取消息队列。这就是 JavaScript 的运行机制，这个过程会不断重复。

消息队列中的消息，可以是用户产生的事件（比如鼠标点击、页面滚动等等），也可以是 IO 事件（比如请求接口、读取文件等等）。只要指定过回调函数，这些事件发生时就会进入消息队列，等待主线程读取。而所谓的回调函数，就是那些会被主线程挂起来的代码。异步任务必须指定回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。

一个异步任务通常是这样的：主线程发起一个异步请求，相应的工作线程（比如浏览器的其他线程）接收请求并告知主线程已收到，然后主线程继续执行后面的代码，同时工作线程执行异步任务；工作线程完成工作后，通知主线程；主线程收到通知后，执行对应的回调函数。而同步执行也是如此，因为它可以被视为没有异步任务的异步执行。

可以这样理解：主线程（JavaScript 线程）只会做一件事，就是从消息队列里面取消息、执行消息，再取消息、再执行。消息队列为空时，就会等待直到消息队列变成非空。只有当前的消息执行结束，才会去取下一个消息。这种机制就叫做事件循环机制 Event Loop，取一个消息并执行的过程叫做一次循环。这时工作线程就是是生产者，主线程就是消费者。工作线程执行异步任务，执行完成后把对应的回调函数封装成一条消息放到消息队列中；主线程不断地从消息队列中取消息并执行，当消息队列空时主线程阻塞，直到消息队列再次非空。

所以，在 JavaScript 中没有任何代码是立刻执行的，但一旦进程空闲就尽快执行。以常见的异步任务定时器为例，setTimeout 和 setInterval 的延时最小间隔是4ms（W3C在HTML标准中规定），而这个间隔表示的是何时将定时器的代码添加到消息队列，而不是何时执行代码。这意味着无论是 setTimeout 还是 setInterval，所设置的时间都只是 n 毫秒被添加到队列中，而不是过 n 毫秒后立即执行。

## 参考文章

[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

[进程与线程的一个简单解释](http://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html)