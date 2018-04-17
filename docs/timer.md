# 定时器

`process.nextTick()`会立即执行回调函数。
微任务队列。

## setImmediate()， clearImmediate()

setImmediate(). It can be passed to clearImmediate() 

`setImmediate()`会在下一轮执行回调函数。

定时器在 IO 操作的回调函数之前执行。

- Timer: `setTimeout`和`setInterval`回调函数。
- I/O callbacks: 处理除了`setTimeout`、`setInterval`、`setImmediate`的回调函数。
- Check: 处理`setImmediate()`指定的回调函数。
- nextTickQueue: 处理`process.nextTick()`的回调函数，不是 event loop 的一部分。
