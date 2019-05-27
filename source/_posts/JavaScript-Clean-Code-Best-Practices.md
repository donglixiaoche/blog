---
title: JavaScript Clean Code - Best Practices
date: 2019-05-27 15:48:50
tags: 
- 翻译
- JavaScript
---

> 本文是一篇翻译过来的文章，原文地址：[https://devinduct.com/blogpost/22/javascript-clean-code-best-practices](https://devinduct.com/blogpost/22/javascript-clean-code-best-practices)

### 介绍

&ensp;&ensp;&ensp;&ensp;如果你关心代码本身以及代码是如何被编写的，而不是仅仅关心代码是否有效，你可以说你练习并且关心整洁的代码。一个专业的开发人员会为未来的自己以及其他人员编写代码，而不仅仅是为了机器。你所编写的任何代码都不会只被编写一次，相反，它会静静地等待下一个接手的人，并且让他头大。希望那个人不是你~

&ensp;&ensp;&ensp;&ensp;基于此，整洁的代码可以被定义为具有以下特征的代码：良好的可读性，便于被人所理解，易于修改和扩展。

&ensp;&ensp;&ensp;&ensp;问问你自己，有多少次你接手别人的工作时你的第一印象时下面的**_"WTF"_** 问题之一：

&ensp;&ensp;&ensp;&ensp;**_"WTF is that?"_**
&ensp;&ensp;&ensp;&ensp;**_"WTF did you do here?"_**
&ensp;&ensp;&ensp;&ensp;**_"WTF is this for?"_**

&ensp;&ensp;&ensp;&ensp;下面这张图片很好地展示了上述内容：
&ensp;&ensp;&ensp;&ensp;![WTF image](https://camo.githubusercontent.com/2050cd696ecddcabad1380b1964c48a60597323e/687474703a2f2f7777772e6f736e6577732e636f6d2f696d616765732f636f6d6963732f7774666d2e6a7067)

&ensp;&ensp;&ensp;&ensp;**_Robert C. Martin(Uncle Bob)_**的一句话应该会让你思考你写代码的方式：
> 虽然糟糕的代码也可以运行，但是如果代码不整洁，它会让开发组织陷入困境。
>(Even bad code can function. But if the code isn’t clean, it can bring a development organization to its knees.)

&ensp;&ensp;&ensp;&ensp;在这边文章中将会把注意力放在JavaScript上，但是文章中所说的一些原则也可以被应用在其他编程语言中。

### 你真正来这阅读的部分-整洁代码 最佳实践

#### 1.强类型检查

使用 === 代替 ==
```javascript
// If not handled properly, it can dramatically affect the program logic. It's like, you expect to go left, but for some reason, you go right.
0 == false // true
0 === false // false
2 == "2" // true
2 === "2" // false

// example
const value = "500";
if (value === 500) {
  console.log(value);
  // it will not be reached
}

if (value === "500") {
  console.log(value);
  // it will be reached
}
```

#### 2.变量
