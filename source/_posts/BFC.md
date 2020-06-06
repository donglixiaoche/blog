---
title: BFC
date: 2020-05-24 16:16:10
tags: CSS
---

Block Formatting Context 块级格式化上下文

<!-- more -->
BFC，CSS里面很重要的一个概念，要知道BFC，首先我们要知道Formatting Content，格式化上下文，在CSS中，所有的东西要么在一个格式化上下文中按照上下文的规则来布局，要么就是本身有一些特殊的布局行为（比如绝对定位）。CSS中，格式化上下文有三种，Block Formatting Context, Inline Formatting Context, Flex Formatting Context。

那么这三种上下文里面中，元素都是按照什么规则来布局的呢？

#### Block Formatting Context
要记住块级上下文的布局规则很容易，只需要记住，根元素`html`创建了一个初始的块级上下文，那一般来说我们页面上的元素都是如何排列的呢？没错，文档流，自上而下，自左向右排列。所以，块级上下文中的排列规则跟正常的文档流一样。

#### Flex Formatting Context
详情参考[FlexBox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

#### Inline Formatting Context
`p`元素会形成一个Inline Formatting Context，

`当某个元素形成一个上下文之后，该元素会包含其所有的子元素，然后整体作为一个单位在外层上下文中排列`。我们经常会用这个特性来解决margin塌陷、父元素中有浮动元素导致高度撑不起来等问题。

除了根元素，还有一些其他的属性可以生成BFC，这里就不列举了...重要的是理解概念，详情参考[MDN文档](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Block_formatting_context)