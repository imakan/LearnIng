> `Nodejs`是基于`chrome`浏览器的`V8`引擎构建的，也就说明他的模型与浏览器类似。我们的js会运行在单个进程的单个线程上。但是从严格意义上来讲。nodejs不是单线程架构，因为他有`I/O`线程，定时器线程等等，只不过这些都是由更底层的`libuv`处理，`libuv`将执行结果放入到队列中等待执行，这就涉及到了`nodejs`的事件循环了。

**事件循环**是尽可能的将相应的操作分担给操作系统，从而让单线程的`nodejs`专注于非阻塞式的`I/O`操作。

# 事件循环阶段

`nodejs`运行，将会初始化事件循环，处理那些通过异步API调用，定时器或者调用process.nextTick()提供的`Script`


`nodejs`分为6个阶段


```javascript
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘ 
```

**注意**：每个框将被称为事件循环的 “阶段”；

结合上图我们可以看出来，每个阶段都会维护一个`FIFO`的可执行队列；
当事件循环进入到某一个阶段，其会执行该阶段的任何操作。直到这个阶段的任务队列为空，或者是回调函数的调用次数达到上限。此时事件循环将会到下一个阶段。

当我们在事件循环的某个阶段执行某个回调时候，该回调可能会生成更多的回调。这些回调都是被添加到对应阶段的队列中。因此，长时间运行的回调可以允许轮询阶段的运行时间远远超过计时器的阀值。


## 各个阶段的介绍

1、`timer`：这个阶段执行通过`setTimeout()`和`setInterval()`设置的回调函数

2、`I/O callback`:执行延迟到下一个循环迭代的`I/O`回调

3、`idle,prepare`：系统调用，也就是`liuv`调用

4、`poll`:轮询阶段，检测新的`I/O`事件，执行与`I/O`相关的回调，（几乎所有的回调都是关闭回调，定时器调度的回调，以及`setImmaditate()`）,node会在此阶段适当的阻塞

5、`check`：此阶段调用`setImmadiate()`设置的回调

6 `close callbacks`：一些关闭回调，比如说`socket.on('close',...)`


#### `setImmediate()` 与 `setTimeout()`

+ `setImmediate()`处于`check`阶段，设计用于在当前轮询阶段后执行脚本

+ `setTimeout()`安排在经过最小阀值后执行的脚本

**注意：** 执行定时器的顺序将根据调用他们的上下文有所不同，如果是从主模块中调用两者，那么时间将会受到进程性能的限制；还有一点如果这两者运行在非`I/O`周期内，这两者的顺序无法保证，如果是在`I/O`周期内的话，`setImmediate()`与优先于`setTimeout()`


#### `process.nextTick()`

从技术上讲，其不属于事件循环的一部分，

那为什么要使用`process.nextTick()`呢？  简单的说，就是在事件循环继续之前做一些事情，比如：如果我们有个需求是在事件循环继续之前，处理用户错误，或者清除任何不需要的资源的话，或者可能再次请求等等



#### `process.nextTick()`与`setImmediate()`

+ `process.nextTick()`在同一个阶段执行

+ `setImmediate()`在事件循环的`check`阶段执行