---
layout:     post
title:      EventLoop
subtitle:   时间循环机制
date:       2020-03-10
author:     ZY
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - Javascript
    - EventLoop
---

# JavaScript执行机制

* javascript是一门单线程语言
* Event Loop是javascript的执行机制
* javascript是按照语句出现的顺序执行的

我们认为的代码是这样的：

```
let a = 1
console.log(a)
let b = 2
console.log(b)
>> 1
>> 2
```

然而实际上代码：

```
setTimeout(function(){
    console.log('定时器开始啦')
});

new Promise(function(resolve){
    console.log('马上执行for循环啦');
    for(var i = 0; i < 10000; i++){
        i == 99 && resolve();
    }
}).then(function(){
    console.log('执行then函数啦')
});

console.log('代码执行结束');
```

按照上面说的‘’按照语句出现的顺序执行", 可能会说打印结果是这样的：

```
//"定时器开始啦"
//"马上执行for循环啦"
//"执行then函数啦"
//"代码执行结束"
```

去浏览器上试一下：

```
>> 马上执行for循环啦
>> 代码执行结束
>> 执行then函数啦
>> 定时器开始啦
```



## 1.关于javascript

javascript是一门**单线程**语言，在最新的HTML5中提出了Web-Worker，但javascript是单线程这一核心仍未改变。所以一切javascript版的"多线程"都是用单线程模拟出来的。

## 2.javascript事件循环

既然js是单线程，那就像只有一个窗口的银行，客户需要排队一个一个办理业务，同理js任务也要一个一个顺序执行。如果一个任务耗时过长，那么后一个任务也必须等着。这就有个问题，当用户想浏览一个新闻网页，但是图片太大，总不能一直让用户在这里等着吧，所有就有了将任务分为下面两类的做法：

* 同步任务
* 异步任务

当用户打开网页，网页的渲染过程就是一大堆同步任务，比如页面骨架和页面元素的渲染。而像加载图片音乐之类占用资源大耗时久的任务，就是异步任务。用下面的图更直观的说明一下：

<img src="/Users/zy/Library/Application Support/typora-user-images/image-20200716230032232.png" alt="image-20200716230032232" style="zoom:50%;" />

导图中的大概意思：

* 同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入Event Table并注册函数。

* 当指定的事情完成时，Event Table会将这个函数移入Event Queue。

* 主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。

* 上述过程会不断重复，也就是常说的Event Loop(事件循环)。

看下代码简单实现：

```
let data = [];
$.ajax({
    url:www.javascript.com,
    data:data,
    success:() => {
        console.log('发送成功!');
    }
})
console.log('代码执行结束');
```

上面是一段简易的`ajax`请求代码：

- ajax进入Event Table，注册回调函数`success`。
- 执行`console.log('代码执行结束')`。
- ajax事件完成，回调函数`success`进入实践队列。
- 主线程从Event Queue读取回调函数`success`并执行。



## setTimeout

我们经常这么实现延时3秒执行：

```
setTimeout(() => {
    console.log('延时3秒');
},3000)
```

`setTimeout`用的地方多了，问题也出现了，有时候明明写的延时3秒，实际却5，6秒才执行函数。

```
setTimeout(() => {
    task();
},3000)
console.log('执行console');

>> 执行console
>> task()
```

```
setTimeout(() => {
   console('2')
},3000)
sleep(10000000)

>> 10000000后 打印 3s执行
```

看完以上代码我们就知道了setTimeout到底是怎么执行的：

* `task()`进入Event Table并注册,计时开始。

* 执行`sleep`函数，很慢，非常慢，计时仍在继续。

* 3秒到了，计时事件`timeout`完成，`task()`进入Event Queue，但是`sleep` 很慢，还没执行完，只好等着。

* `sleep`终于执行完了，`task()`终于从Event Queue进入了主线程执行。

经过指定时间后，把要执行的任务加入到Event Queue中，又因为是单线程任务要一个一个执行，如果前面的任务需要的时间太久，那么只能等着，导致真正的延迟时间远远大于3秒。



我们还经常遇到`setTimeout(fn,0)`这样的代码，0秒后执行又是什么意思呢？是不是可以立即执行呢？

答案是不会的，`setTimeout(fn,0)`的含义是，指定某个任务在主线程最早可得的空闲时间执行，意思就是不用再等多少秒了，只要主线程执行栈内的同步任务全部执行完成，栈为空就马上执行。举例说明：

```
setTimeout(() => {
    console.log('执行啦')
},0);
console.log('结束了')
```

`setTimeout` 0ms，即便主线程为空，0毫秒实际上也是达不到的。根据HTML的标准，最低是4毫秒

## Promise与process.nextTick(callback)

传统的定时器我们已经了解过了，接着我们看一下`Promise`与`process.nextTick(callback)`。

`Promise`的定义和功能本文不再赘述，不了解的读者可以学习一下阮一峰老师的[Promise](http://es6.ruanyifeng.com/#docs/promise)。而`process.nextTick(callback)`类似node.js版的"setTimeout"，在事件循环的下一次循环中调用 callback 回调函数。

除了广义的同步任务和异步任务，对任务还有更精细的定义：

- macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
- micro-task(微任务)：Promise，process.nextTick

不同类型的任务会进入对应的Event Queue，比如`setTimeout`和`setInterval`会进入相同的Event Queue。

事件循环的顺序，决定js代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。听起来有点绕，我们用文章最开始的一段代码说明：

```
setTimeout(function() {
    console.log('setTimeout');
})

new Promise(function(resolve) {
    console.log('promise');
}).then(function() {
    console.log('then');
})

console.log('console');
```



* 这段代码作为宏任务，进入主线程。

* 先遇到`setTimeout`，那么将其回调函数注册后分发到宏任务Event Queue。(注册过程与上同)

* 接下来遇到了`Promise`，`new Promise`立即执行，`then`函数分发到微任务Event Queue。

* 遇到`console.log()`，立即执行。

* 整体代码script作为第一个宏任务执行结束，看看有哪些微任务, 发现了`then`在微任务Event Queue里面，执行。

* 第一轮事件循环结束了，我们开始第二轮循环，当然要从宏任务Event Queue开始。我们发现了宏任务Event Queue中`setTimeout`对应的回调函数，立即执行。

* 结束。

事件循环，宏任务，微任务的关系如图所示：

![image-20200716233737647](/Users/zy/Library/Application Support/typora-user-images/image-20200716233737647.png)





我们来分析一段较复杂的代码，看看你是否真的掌握了js的执行机制：



```
1 7 6 8  2  4 3 5  9 11 10 12


console.log('1');
setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})


setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})

```

第一轮事件循环流程分析如下：

- 整体script作为第一个宏任务进入主线程，遇到`console.log`，输出1。
- 遇到`setTimeout`，其回调函数被分发到宏任务Event Queue中。我们暂且记为`setTimeout1`。
- 遇到`process.nextTick()`，其回调函数被分发到微任务Event Queue中。我们记为`process1`。
- 遇到`Promise`，`new Promise`直接执行，输出7。`then`被分发到微任务Event Queue中。我们记为`then1`。
- 又遇到了`setTimeout`，其回调函数被分发到宏任务Event Queue中，我们记为`setTimeout2`。

| 宏任务Event Queue | 微任务Event Queue |
| ----------------- | ----------------- |
| setTimeout1       | process1          |
| setTimeout2       | then1             |

- 上表是第一轮事件循环宏任务结束时各Event Queue的情况，此时已经输出了1和7。
- 我们发现了`process1`和`then1`两个微任务。
- 执行`process1`,输出6。
- 执行`then1`，输出8。

第一轮事件循环正式结束，这一轮的结果是输出1，7，6，8。那么第二轮时间循环从`setTimeout1`宏任务开始：

首先输出2。接下来遇到了`process.nextTick()`，同样将其分发到微任务Event Queue中，记为`process2`。`new Promise`立即执行输出4，`then`也分发到微任务Event Queue中，记为`then2`。

| 宏任务Event Queue | 微任务Event Queue |
| ----------------- | ----------------- |
| setTimeout2       | process2          |
|                   | then2             |

* 第二轮事件循环宏任务结束，我们发现有`process2`和`then2`两个微任务可以执行。

* 输出3。

* 输出5。

* 第二轮事件循环结束，第二轮输出2，4，3，5。

* 第三轮事件循环开始，此时只剩setTimeout2了，执行。

* 直接输出9。

* 将`process.nextTick()`分发到微任务Event Queue中。记为`process3`。

* 直接执行`new Promise`，输出11。

* 将`then`分发到微任务Event Queue中，记为`then3`。

| 宏任务Event Queue | 微任务Event Queue |
| ----------------- | ----------------- |
|                   | process3          |
|                   | then3             |

- 第三轮事件循环宏任务执行结束，执行两个微任务`process3`和`then3`。
- 输出10。
- 输出12。
- 第三轮事件循环结束，第三轮输出9，11，10，12。

整段代码，共进行了三次事件循环，完整的输出为1，7，6，8，2，4，3，5，9，11，10，12。


