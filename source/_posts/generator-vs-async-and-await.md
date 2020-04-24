---
title: Generator Vs Async & Await
date: 2020-04-24 16:36:15
tags:
---

一直说es7中的async和await是es6的generator的语法糖，今天尝试写下...
<!-- more -->
参考资料: [Ponyfoo ES6 Generator in Depth](https://ponyfoo.com/articles/es6-generators-in-depth)

```javascript
let a = Promise.resolve(1)
let b = Promise.resolve(2)
let c = Promise.resolve(3)

function myAsync(myAwait) {
  let g = myAwait()
  let result = g.next()

  call(result)
  function call(task) {
    if(task.done === true) return
    task.value.then(res => {
      call(g.next(res))
    })
  }
}

function* myAwait() {
  let a1 = yield a
  console.log(a1, 1)
  let b1 = yield b
  console.log(b1, 2)
  let c1 = yield c
  console.log(c1, 3)
}

myAsync(myAwait)
```
这里，封装好了myAsync之后，只需要在myAwait里面同步地写异步代码就好了(a、b、c)

