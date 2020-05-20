---
title: Koa2
date: 2020-04-28 16:46:58
tags: Nodejs
---

稍微看了下Koa2的源码。
<!-- more -->

简单来说Koa2就是对node.js中`http`模块的一次封装，其中比较重要的机制就是中间件，所谓的洋葱模型，这里来解释下。
```javascript
function compose (middleware) {
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }

  return function (context, next) {
    // last called middleware #
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```
这段代码是koajs下一个单独的库，(compose)[https://github.com/koajs/compose]，洋葱模型的核心。

每次调用`app.use`时，实际都是往`middle`数组里面push一个函数，最终koa所做的就是把这些函数串起来，放到http.createServer的回调函数里面。

这里我们来看，koa是怎么把这些函数串起来的，可以看到，所有的返回结果，都用`Promise.resolve`或者`Promsie.reject`封装起来了，最终的整个封装后的函数返回的结果就是第一个中间件的返回结果。每调用一个中间件时，我们会把下一个中间件作为第二个参数传递进去（注意是封装过的，返回值为`promise`，所以如果想等待后面的中间件处理完后再返回结果，需要`await`）。


感觉没啥需要说的了...提几个问题来确认下理解：
- 为啥在一个中间件里面多次调用下一个中间件会报错？
  可以看到，每个中间件都是绑定了一个`i`的，通过函数传参传进去的，第一个中间件是0，第二个中间件是1，并且，在每个函数的闭包内，都会有一个index变量，调用第一个中间件的时候，这个变量是-1，调用第二个中间件的时候，这个变量是0，调用第三个中间件的时候是1...检查完成后没问题，就会把这个变量加一，如果在我们连续调用2次中间件，第一次调用后，index会加一，自然会导致后面调用时
  ```javascript
   if (i <= index) return Promise.reject(new Error('next() called multiple times'))
  ```
  这一句检查通不过，自然就报错啦。

