---
title: Truly reactive programming with Svelte3.0
date: 2019-05-30 15:18:47
categories: 
- 翻译
tags: 
- 翻译
- 新鲜货
---

> 本文是一篇翻译过来的文章，原文地址：[https://blog.logrocket.com/truly-reactive-programming-with-svelte-3-0-321b49b75969](https://blog.logrocket.com/truly-reactive-programming-with-svelte-3-0-321b49b75969)

![Svelte](https://cdn-images-1.medium.com/max/1200/1*2hDcp8VSEJk74trofc3JtQ.jpeg)

这个标题有点戏剧性，但是话又说回来，Svelte背后的想法正是如此。如果你还不知道Svelte是什么，那赶紧过来瞅瞅 — 你将会看到一场革命，并且Svelte将会取得最后的胜利（并不是在对Svelte团队施加压力哈:sweat_smile:）。

<!-- more -->

请注意本文并不是学习和使用Svelte的教程。Svelte团队已经提供了一份很棒的[一步一步的互动式](https://svelte.dev/tutorial/basics)教程，以便帮助你走进反应式编程的世界。

**免责声明**：我不是编程界的摇滚明星，我不可能知道所有事情。我只是对每天发生的新鲜事情感到好奇，并且在我可以的时候分享- 于是就有了这篇文章。请带着一些怀疑的想法来看待这里我所说的事情，并且，如果我说了一些愚蠢的话，麻烦及时让我知道。

好嘞！让我们开始吧！:boom:

## 首先，关于React
在我开始讨论为什么Svelte如此强大之前，我们先来看看Dan的一条推文，一段时间后我们再来看看其中表达的意思：

<blockquote class="twitter-tweet" data-lang="zh-cn"><p lang="en" dir="ltr">React is not fully reactive</p>&mdash; Dan Abramov (@dan_abramov) <a href="https://twitter.com/dan_abramov/status/1025801430668664833?ref_src=twsrc%5Etfw">2018年8月4日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

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

乍一看，这段代码似乎依然是从上到下执行的，但是仔细看的话，你会发现实际上代码执行时会有一些跳转。

当代码执行到第四行时，编译器发现`firstNumber`并没有被定义，于是编译器暂停解释该语句，并且在你的代码中寻找该变量的定义。当找到`firstNumber`在第五行被定义后，第五行会先于第四行执行，当第五行执行完毕后，再返回执行第四行。

_**TL;DR：如果语句A依赖于语句B，不管语句声明的顺序如何，B将先于A执行**_

Svelte是如何应用上述的原理以实现真正的反应式呢？在Svelte中，你可以用一个标识符来表示一个语句，像这样：`$: foo = bar`。这样做之后，语句`foo = bar`就有了一个表示`$`（上述语句在严格模式下会报错）

当Svelte看到任何有`$`前缀的语句时，编译器就知道左边的变量由右边的变量导出。于是，现在我们有了将一个变量绑定在另一个变量的方法。

Reactivity!(反应式!)这意味着现在我们用JavaScript的原生API来实现真正的响应式，而无需再摆弄一些三方API，比如`this.setState`。

下面的代码是这样写的实际效果：

```javascript
// vanilla js
let foo = 10;
let bar = foo + 10; // bar is now 20
foo = bar // bar is still 20 (no reactivity)
bar = foo + 5 // now bar becomes 25
// svelte js
let foo = 10;
$: bar = foo + 10; // bar is now 20
foo = 15 // bar is now 25 because it is bound to the value of foo
```

在上述的Svelte代码中，我们并不需要将`foo`的新值重新赋值给`bar`，代码会帮我们自动处理。当你把`foo`的值更改为15时，`bar`会自动更新为25。

上述的Svelte代码经过编译后，大概便变成这样：
```javascript
... omitted for brevity ...
function instance($$self, $$props, $$invalidate) {
   let foo = 10; // bar is now 20
   $$invalidate('foo', foo = 15) // bar is now 25 because it is bound to the value of foo
   let bar;
   $$self.$$.update = ($$dirty = { foo: 1 }) => {
     if ($$dirty.foo) { $$invalidate('bar', bar = foo + 19); }
   };
   return { bar };
 }
... omitted for brevity ...
```
**花点时间认真看看上面的代码，真的，认真看下。**

你看到`foo`是如何更新的吗？即使是在`bar`没有被定义之前。（我暂时没看懂...）这是因为Svelte的编译器以拓扑的顺序来执行代码，而不是自上而下。

Svelte自动对数据的更新做出改变，而不需要用户关系改变的内容和时间，这一切都是自动进行的。用户可以专注于代码逻辑，让Svelte根据应用状态的改变来调节UI。

### 简洁

记得我说过Svelte可以用更少的代码来做到相同的工作吗？我是认真的！看看下面这个组件分别用Svelte和React实现的版本：

![Svelte & React](https://i1.wp.com/cdn-images-1.medium.com/max/1600/1*y8KNWov5UwulVryUPq6srw@2x.png?ssl=1)

__**17行代码 vs 29行代码**__

上述两个应用实现的功能几乎完全一样，但是你可以看到在`React.js`中，我们不得不编写更多地代码 ---- `Angular`里面更过分。

![Svelte & React](https://i2.wp.com/cdn-images-1.medium.com/max/1600/1*129rCsjvqwpczQ5Ld_U44A@2x.png?ssl=1)

Svelte简洁的代码除了看起来更方便之外，同时也更利于逻辑的判断和推理，因为代码中没有其他冗余的部分。我们不需要事件处理程序来更新input输入--简单的绑定就可以了。

假如你是一个刚刚入门的web开发者，哪一种方式学习起来更加简单？左边？还是右边？

或许你认为仅仅是少一点代码看起来很微不足道，但是当你所负责的项目变得越来越大，越来越复杂时，简洁的代码就变得很重要。我个人发现，有时候需要花费数个小时才能理解一个同事写的`React`组件。

### 性能
OK，我们已经见识到了Svelte如何实现真正的反应式，并且让我们的代码更加简短和简洁。那么性能呢？如果用Svelte来编写完整的应用，用户体验如何？

一个让`React`如此强大的原因是`React`使用虚拟DOM来更新UI中很少的一部分，避免了每次数据改变时都重绘整个DOM。但是，这种方法有一个缺点是，当一个`React`组件改变时，`React`会重新渲染所有的子组件，不管子组件是否有重新渲染的必要。这就是为什么`React`提供了`shouldComponentUpdate`，`useMemo`，`React.PureComponent`等API。只要使用虚拟DOM来更新UI，这个问题就会一直存在。

Svelte没有使用虚拟DOM，那么她是如何根据应用的状态来同步更新UI的呢？让我再次引用Rich Harris的精彩YGLF演讲：

> Frameworks are not tools for organizing your code. They are tools for organizing your mind.
> 框架不是组织代码的工具，框架是用来组织你的思想的。

正是上面的引用给Rich带来了框架能够在构建阶段运行的灵感，这样代码在运行时就不需要其他中介。这个灵感也导致了Svelte是编译器，而不是框架。

这个简单的想法正是Svelte如此快速的原因。Svelte将你的代码编译为更底层的高效的代码，直接与DOM交互。

上面这些都很棒，但是Svelte是如何避免数据改变时重绘整个DOM呢？Svelte与React的差异在于如何感知数据的改变。我们已经知道，`React`依赖于用户调用API来感知数据改变，而`Svelte`只需要简单的使用`=`就可以了。

如果一个变量---我们假设`foo`---通过`=`进行了更新，Svelte只会更新其他依赖于`foo`的变量。这允许Svelte只更新部分从`foo`变量中获取值的DOM。

关于其中的细节，可以看[Rich的解释](https://www.youtube.com/watch?v=AdNJ3fydeao)

## 最后一点想法
Svelte3.0是一段时间内发生的关于软件开发的最好的事情。有些人可以说这有些夸大其词，但是我不这么认为。Svelte背后的理念使得我们在向浏览器中引用更少的模板的同时，做更多的事情。应用也会因此变得更加高性能，更加轻便，同时代码也变得更加优雅和易读。那现在是不是Svelte可以马上取代React，Angular，Vue或者其他前端框架吗？

到目前为止，答案是否定的。相比于这些框架，Svelte相对来说还不是很成熟。它需要时间去成长，变得更加成熟，甚至解决一些目前我们还没有发现的问题。

就像`React`横空出世改变软件开发模式一样，Svelte也有可能改变我们关于框架的看法，在我们创建新的项目时提供无限可能。

最后，**Happy Coding!**