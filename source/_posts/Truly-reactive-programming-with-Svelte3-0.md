---
title: Truly reactive programming with Svelte3.0
date: 2019-05-30 15:18:47
categories: 
- 翻译
tags: 
- 翻译
- 新鲜货
---

> 本文是一篇翻译过来的文章，原文地址：[https://blog.logrocket.com/truly-reactive-programming-with-svelte-3-0-321b49b75969](hhttps://blog.logrocket.com/truly-reactive-programming-with-svelte-3-0-321b49b75969)

![Svelte](https://cdn-images-1.medium.com/max/1200/1*2hDcp8VSEJk74trofc3JtQ.jpeg)

这个标题有点戏剧性，但是话又说回来，Svelte背后的想法正是如此。如果你还不知道Svelte是什么，那赶紧过来瞅瞅 — 你将会看到一场革命，并且Svelte将会取得最后的胜利（并不是在对Svelte团队施加压力哈:sweat_smile:）。

<!-- more -->

请注意本文并不是学习和使用Svelte的教程。Svelte团队已经提供了一份很棒的[一步一步的互动式](https://svelte.dev/tutorial/basics)教程，以便帮助你走进反应式编程的世界。

**免责声明**：我不是编程界的摇滚明星，我不可能知道所有事情。我只是对每天发生的新鲜事情感到好奇，并且在我可以的时候分享- 于是就有了这篇文章。请带着一些怀疑的想法来看待这里我所说的事情，并且，如果我说了一些愚蠢的话，麻烦及时让我知道。

好嘞！让我们开始吧！:boom:

## 首先，关于React
在我开始讨论为什么Svelte如此强大之前，我们先来看看Dan的一条推文，一段时间后我们再来看看其中表达的意思：
<!-- todo 这里需要加一张推文图片 -->
![react is not fully reactive](http://pryb8y3zu.bkt.clouddn.com/react-is-not-fully-reactive.png)

**另一份免责声明**：这篇文章也不是以任何方式批评和攻击React。我只是简单地决定用React作为一个学习案例，因为此文的大部分读者应该都用过一点React。React是跟Svelte对比的一个很好地案例。

Dan到底想表达什么，这对我们现在的编程方式有何影响呢？在回答这个问题前，让我简单地向你介绍下React背后的工作原理。

当你渲染一个React应用时，React保存了一份DOM的拷贝，称作[虚拟DOM](https://reactjs.org/docs/faq-internals.html)。虚拟DOM充当你的React代码和浏览器渲染DOM之间的中间人。

每当你的数据改变时（比如当你调用`this.setState`，`useState`），React做了一点工作来决定如何重绘屏幕上的UI。数据更新后，React会对比虚拟DOM和真实DOM，并且只更新两者间不同的地方。这样就避免了每次数据改变时都要重新渲染整个DOM。

现在，一切都变得很迅速，因为更新虚拟DOM比更新真实DOM所耗费的性能小很多，React只更新了真实DOM确实需要更新的部分。[这篇](https://medium.com/@gethylgeorge/how-virtual-dom-and-diffing-works-in-react-6fc805f9f84e)文章很好地讲述了整个过程。

但是！但是啊，聪明的你可能已经注意到了一些事情。那就是，如果你不告诉React你的数据发生了改变（比如，通过调用`this.setState`或者钩子函数），你的虚拟DOM就不会发生改变，React就不再react了。（ba dum tss! 🤓）（这句翻译不来...有点像 dumb ass...）

这就是Dan为什么说React不是完全reactive的原因。React依赖用户来跟踪应用中的数据，通过用来告诉自己数据发生了改变，但这对用户来说通常是额外的工作。


## OK，让我们来看看Svelte

Svelte提供一种全新的方式来搭建超级快速，高效，真正响应式行为的UI。没有虚拟DOM，并且代码行数比所有其他你用过框架或者包更少。

这听起来很棒，但是它与众多的JavaScript框架有什么不同呢？Let me show you :smirk:

### 真正的反应式

Svelte不是一个包或者框架。Svelte是一个编译器，它接受原生的JavaScript代码，输出的Javascript也是直接与DOM交互，而不需要任何其他东西充当中介。

等一下？什么？一个编译器？（作者戏真多...）。 是的----- 一个编译器。这是一个非常棒的想法，我不明白为什么直到现在Svelte还不是很惹人注意，现在，让我来告诉你为什么Svelte是那么地酷炫。

以下是[Rich Harris在YGLF 2019年会议上的讲话](https://www.youtube.com/watch?v=AdNJ3fydeao)：

> Svelte 3.0将反应性从组件API移入语言中。
>(Svelte 3.0 moves reactivity out of the component API and into the language.)

这句话是什么意思呢？我们已经知道了React（以及很多其他的前端框架）需要你调用特定的API来告诉其数据发生了改变（通过调用`this.setState`或者`useState`）才能更新它的虚拟DOM。在React中，需要调用`this.setState`意味着你的应用的反应性其实是绑定在特定的API上，没有这些API，数据的改变并不会被注意到。

而Svelte通过另一种方式来实现反应性。

Svelte从[Observable]()运行代码的方式中获取到了灵感，以拓扑代替从上至下的方式来运行代码。我们通过下面这段代码来看看什么是通过拓扑顺序来执行代码：
```javascript
(() => {
   const square => number => number * number;

   const secondNumber = square(firstNumber);
   const firstNumber = 42;

   console.log(secondNumber);
   })();
```

如果你从上至下运行这段代码的话，解释到在第4行的时候，程序会报错。因为`secondNunmber`依赖于`firstNumber`，但`firstNumber`还没有被定义。但是如果程序以拓扑的方式来执行的话，程序不会报任何错误。为什么呢？以为编译器不会自上而下执行代码，而是会查看所有的变量从而生成一个依赖图（即谁被需要谁先被执行）

在上述的例子中，要理解编译器如果以拓扑的方式编译代码很简单：
1. `square`变量依赖其他变量吗？
-没有，初始化`square`
2. `secondNumber`依赖于其他变量吗？
-`secondNumber`依赖于`square`和`firstNumber`,`square`已经被初始化，但是`firstNumber`还没有被初始化，初始化`firstNumber`
3. OK，`firstNumber`已经被初始化，现在可以用`firstNumber`和`square`来初始化`secondNumber`
-还有其他的变量需要初始化吗？没有-执行代码