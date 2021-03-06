---
title: Webpack插件机制和Tapable
date: 2020-04-20 20:02:11
tags:
---

webpack的插件机制困扰了我一段时间，只知道使用了开源库[tapable]()，但是稍微看了下，被几个概念弄得头晕就放弃了，今天又稍微看了下，感觉清晰了点。

<!-- more -->

## 几个概念间的区别和联系

现在想来其实很简单，不知道当时为啥看不懂。插件，plugin，就是符合特定接口的对象等等。一个系统想要提供插件来为用户提供扩展功能，做法就是定义好插件的接口，也就是抽象。符合该接口的对象，都可以作为插件来让用户对系统进行的扩展。比如在webpack中，编写一个插件，只需要定义一个对象，并且对象的原型链中包含apply方法就好了(webpack的插件也可以直接是一个function)。

那hooks又是什么嘞？在系统工作的过程中，比如webpack，会进行一些列的操作，譬如解析代码、生成AST、输出目标代码等等，只是定义一个宽泛的接口方法，不足以满足用户的需求。如果编写插件的用户想要在某个指定的阶段来执行插件怎么办？想要等AST生成后再执行插件咋办？这就是tapable提供的内容，使用tapable在我们的系统中定义一系列的hook，让插件可以选择性地hook in，让插件选择性地挂在到系统执行地指定阶段，从而更好地编写插件的功能。

总结就是:

- Plugin: 符合系统描述地特性接口的类、对象等等。
- hook：系统暴露的一些点，代码执行过程中的特定的阶段，告诉用户可以更具体地使插件在某个阶段执行，webpack中使用tapable来定义一系列地hooks。

-----------2020/05/07更新------------

稍微看了下Tapable和webpack的文档，总结下tapable可以定义的hook类型以及Webpack的Compiler定义的钩子。

首先说明，Tapable提供的hook可以挂载(hook in)多个函数，比如Webpack中，不同的插件可以挂载到同一个钩子上.那么同一个钩子中的函数是按照什么顺序执行的呢？Tapable提供不同的钩子类型来区分。

- 基本的钩子类型
  钩子上挂载的函数按照挂载的先后顺序来执行
- Waterfall
  跟基本钩子类型的执行顺序一样，只不过会把上一个挂载函数的返回值传入下一个挂载的函数
- Bail
  Bail类型的hook允许中途退出，任何挂载在Bail hook上的函数返回不为`undefined`的值的话，剩余的挂载在这个钩子上的函数都不会执行
- Loop
  挂载的函数返回值不是`undefined`的话，会从头重新执行此钩子上挂载的所有函数