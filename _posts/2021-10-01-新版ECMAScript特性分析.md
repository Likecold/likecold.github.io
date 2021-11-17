---
layout:     post
title:      新版 ECMAScript 特性分析
subtitle:   特性分析
date:       2021-10-01
author:     ZY
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - ECMAScript
    - Javascript
---

## 新版 ECMAScript 特性分析
本文约定： 示例代码中注释里双右尖括号 `>>` 代表“打印/输出”的意思。

### 1.0 本篇导读

JavaScript是一门支持多种编程范式的语言，灵活性很高，功能也十分强大。因为灵活性，JavaScript有很多种使用方式，被应用到浏览器、客户端软件、嵌入式硬件等多种场景下。在面向对象（OOP）这种编程范式大行其道的今天，这种变化多端的语言因其编程范式多样，每种场景下使用形态各异，使用者之间思维差别大，见解又各不相同，因此也就被误解得极深。正是由于这种相对的复杂性，前端工程师们有必要更系统、深入地对这门语言进行学习和研究。

现在，JavaScript在TC39（TC39由包括浏览器厂商在内的各方组成，他们开会推动JavaScript特性的提案沿着一条严格的流程推进）的推动下正加速地迭代更新，每年都会有一些新特性出现。前端工程师们有必要了解一下这些新特性，以便更好地在一线互联网企业发挥出自己的能量。

### 1.1 JavaScript 和 ECMAScript 6 的关系

JavaScript起源于1995年Netscape为自家游览器创造的脚本语言LiveScript。后来因为Sun公司的Java语言兴起，Netscape为了沾热度将LiveScript改名为JavaScript，但其实Java和JavaScript没有一点关系。

1997年Netscape将JavaScript规范提交到 Ecma International（[www.ecma-international.org](https://www.ecma-international.org/)，简称EI），诞生了ECMAScript （简称“ES”）第1个版本。

随后在1999年诞生了十分稳定的ES 3，也就是2009年12月之前前端工程师们最广泛使用的版本。

在2009年12月诞生了ES 5。你可能会问ES 4 呢，抱歉，版本4因为太激进，EI委员会成员之间的意见不一，最后胎死腹中，改由更加平滑温和的版本5继承版本4的一些新特性和功能。

EI于2015年6月发布了ES 6，也就是我们今天广泛使用的版本。这之后每年6月发布一个极小更新的ES版本，比如2016年6月发布的 ES 2016 叫做ES 7，而这个版本只增加了两个新特性。截止本书编写时，ES 11的提案已经定稿了。

综上可见，**JavaScript是ECMAScript规范的一种实现**。为方便读者理解交流，而且由于ECAMScript 6 之后变化并不是特别大，如果没有特别说明，本书后面讲述的JavaScript对应的规范默认为 ES 6。

本文下面会介绍ES 7~11的新特性。

### 1.2 ES 7 新特性

| 新特性 | 中文说明 |
| :--- | :--- |
| `Array.prototype.includes` |  |
| Exponentiation operator | 指数函数的中缀表示法 |

#### Array.prototype.includes

includes是一个Array上很有用的方法，用于快速查找数组中是否包含某个元素，包括NaN（所以和indexOf不一样\)。

```javascript
(() => {
  let arr = [1, 2, 3, NaN];

  if (arr.includes(2)) {
    //查找2是否存在于arr数组中
    console.log("找到了!"); //>> 找到了！
  }
  if (!arr.includes(2, 3)) {
    //第二个参数3表示数组下标为3的项，也即第4项开始查找
    console.warn("不存在!"); //>> 不存在！
  }

  //下面两句说明incluedes和indexOf的区别
  console.log(arr.includes(NaN)); //true
  console.log(arr.indexOf(NaN) != -1); //false
})();
```

#### 指数函数的中缀表示法

这个是与`Math.pow`有关的特性，还记得`i++`,`x += x`这种写法吗，指数函数的中缀表示法与之类似。与python语言一样，JavaScript也采用两个星符号`**`来表示`Math.pow`。好处有两个：  
a. 中缀表示法比函数表示法更简洁，这使它更可取。  
b. 方便数学、物理、机器人学等领域的计算。

用法示例如下：

```javascript
//用法一：x ** y

let squared = 2 ** 2;//等同于: 2 * 2
let cubed = 2 ** 3;//等同于: 2 * 2 * 2
```

```javascript
//用法二：x **= y

let a = 2;
a **= 2;//等同于: a = a * a;

let b = 3;
b **= 3;//等同于: b = b * b * b;
```

没错，是一块很甜的**语法糖**（指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用）。

### 1.3 ES 8 新特性

| 新特性 | 中文说明 |
| :--- | :--- |
| `Object.values` / `Object.entries` |  |
| String padding | 字符串追加 |
| `Object.getOwnPropertyDescriptors` |  |
| Trailing commas in function parameter lists and calls | 结尾允许逗号 |
| Async functions | 异步函数 |
| Shared memory and atomics | 共享内存和 Atomics 对象 |

#### Object.values\(\) / Object.entries

用法是 `Object.values(obj)`，`obj` 可以是一个对象或者数组。

```javascript
const obj = { x: 'xxx', y: 1 };
Object.values(obj); // ['xxx', 1]

const obj = ['e', 's', '8']; // 等同于 { 0: 'e', 1: 's', 2: '8' };
Object.values(obj); // ['e', 's', '8']

//当把数字当做对象的键的时候，返回的数组以键的值升序排序
const obj = { 10: 'xxx', 1: 'yyy', 3: 'zzz' };
Object.values(obj); // ['yyy', 'zzz', 'xxx']
Object.values('es8'); // ['e', 's', '8']
```

`Object.entries`方法返回一个给定对象可枚举属性值的数组\[key, value\]，与`Object.values`类似。

```javascript
const obj = { x: 'xxx', y: 1 };
Object.entries(obj); // [['x', 'xxx'], ['y', 1]]

const obj = ['e', 's', '8'];
Object.entries(obj); // [['0', 'e'], ['1', 's'], ['2', '8']]

const obj = { 10: 'xxx', 1: 'yyy', 3: 'zzz' };
Object.entries(obj); // [['1', 'yyy'], ['3', 'zzz'], ['10', 'xxx']]
Object.entries('es8'); // [['0', 'e'], ['1', 's'], ['2', '8']]
```

#### 字符串追加

在 ES 8 中String新增了两个实例函数`String.prototype.padStart`和`String.prototype.padEnd`，允许将空字符串或其他字符串添加到原始字符串的开头或结尾。

* `String.padStart(targetLength,[padString])`  
  _targetLength：_当前字符串需要填充到的目标长度。如果这个数值小于当前字符串的长度，则返回当前字符串本身。

  _padString：_\(可选\)填充字符串。如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断，此参数的缺省值为空格。

* `String.padEnd(targetLength,padString])` 参数释义同上。

```javascript
'es8'.padStart(2);          // 'es8'
'es8'.padStart(5);          // '  es8'
'es8'.padStart(6, '1891');  // '189es8'
'es8'.padStart(14, 'coffe');  // 'coffecoffeces8'
'es8'.padStart(7, '0');     // '0000es8'

'es8'.padEnd(2);            // 'es8'
'es8'.padEnd(5);            // 'es8  '
'es8'.padEnd(6, '1891');    // 'es8189'
'es8'.padEnd(14, 'coffe');    // 'es8coffecoffec'
'es8'.padEnd(7, '9');       // 'es89999'
```

#### Object.getOwnPropertyDescriptors

`getOwnPropertyDescriptors`方法返回一指定对象自己所有的属性内容，并且属性内容只是自身直接定义的，而不是从object的原型继承而来的。  
定义是：`Object.getOwnPropertyDescriptors(obj)`，`obj` 是指目标对象，这个方法返回的值可能是 configurable、enumerable、writable、get、set 和 value。

```javascript
const obj = { 
  get es7() { return 7; },
  get es8() { return 8; }
};
Object.getOwnPropertyDescriptors(obj);
// {
//   es7: {
//     configurable: true,
//     enumerable: true,
//     get: function es7(){}, //the getter function
//     set: undefined
//   },
//   es8: {
//     configurable: true,
//     enumerable: true,
//     get: function es8(){}, //the getter function
//     set: undefined
//   }
// }
```

#### 结尾允许逗号

用法示例如下：

```javascript
//定义参数时
function foo(
    param1,
    param2,//结尾逗号
) {}
//传参时
foo(
    'coffe',
    '1891',//结尾逗号
);
//对象中
let obj = {
    "a": 'coffe',
    "b": '1891',//结尾逗号
};
//数组中
let arr = [
    'coffe',
    '1891',//结尾逗号
];
```

这样改动的好处有两点：

* 重新排列项目更简单，因为如果最后一项更改其位置，则不必添加和删除逗号。
* 可以帮助git等版本控制系统跟踪实际发生的变化，在查看代码改动、合并代码的时候，少报一些无用信息。例如，从

```javascript
[
    'coffe'
]
```

变更为

```javascript
[
    'coffe',
    '1891'
]
```

在git里它会报同时修改了两行代码，采用结尾逗号`'coffe',`之后，就只会报仅有一行代码`'1891',`的变动，这样做代码review（代码复查）的时候就更省眼力了。

#### 异步函数

Async Functions也就是我们常说的Async/Await，相信大家对于这个概念都已经不陌生了。Async/Await是一种用于处理JS异步操作的语法糖，可以帮助我们摆脱**回调地狱**（callback hell），编写更加优雅的代码。

通俗的理解，`async`关键字的作用是告诉编译器对于标定的函数要区别对待。当编译器遇到标定的函数中的`await`关键字时，要暂时停止运行，等到`await`标定的函数处理完毕后，再进行相应操作。如果该函数fulfiled了，则返回值是fulfillment value，否则得到的就是reject value。

下面通过拿普通的promise写法来对比，就很好理解了：

```javascript
async function asyncFunc() {
    const result = await otherAsyncFunc();// otherAsyncFunc()返回一个Promise对象
    console.log(result);
}

// 等同于:
function asyncFunc() {
    return otherAsyncFunc()// otherAsyncFunc()返回一个Promise对象
    .then(result => {
        console.log(result);
    });
}
```

按顺序处理多个异步函数的时候优势更为明显：

```javascript
async function asyncFunc() {
    const result1 = await otherAsyncFunc1();// otherAsyncFunc1()返回一个Promise对象
    console.log(result1);
    const result2 = await otherAsyncFunc2();// otherAsyncFunc2()返回一个Promise对象
    console.log(result2);
}

// 等同于:
function asyncFunc() {
    return otherAsyncFunc1()// otherAsyncFunc1()返回一个Promise对象
    .then(result1 => {
        console.log(result1);
        return otherAsyncFunc2();// otherAsyncFunc2()返回一个Promise对象
    })
    .then(result2 => {
        console.log(result2);
    });
}
```

并行处理多个异步函数：

```javascript
async function asyncFunc() {
    const [result1, result2] = await Promise.all([
        otherAsyncFunc1(),// otherAsyncFunc1()返回一个Promise对象
        otherAsyncFunc2() // otherAsyncFunc2()返回一个Promise对象
    ]);
    console.log(result1, result2);
}

// 等同于:
function asyncFunc() {
    return Promise.all([
        otherAsyncFunc1(),// otherAsyncFunc1()返回一个Promise对象
        otherAsyncFunc2() // otherAsyncFunc2()返回一个Promise对象
    ])
    .then([result1, result2] => {
        console.log(result1, result2);
    });
}
```

处理错误：

```javascript
async function asyncFunc() {
    try {
        await otherAsyncFunc();// otherAsyncFunc()返回一个Promise对象
    } catch (err) {
        console.error(err);
    }
}

// 等同于:
function asyncFunc() {
    return otherAsyncFunc()// otherAsyncFunc()返回一个Promise对象
    .catch(err => {
        console.error(err);
    });
}
```

Async Functions若是要展开讲可以占很大的篇幅，鉴于本篇是一篇介绍性文章，故此不再进行深入论述。

#### 共享内存和 Atomics 对象

SharedArrayBuffer 对象用来表示一个通用的，固定长度的原始二进制数据缓冲区，类似于 ArrayBuffer对象（如果之前你没有接触过ArrayBuffer相关知识的话，建议从[内存管理速成教程系列漫画解说入门](https://hacks.mozilla.org/2017/06/a-cartoon-intro-to-arraybuffers-and-sharedarraybuffers/)） ，它们都可以用来在共享内存（shared memory）上创建视图。与 ArrayBuffer 不同的是，SharedArrayBuffer 不能被分离。

```javascript
/**
 * 
 * @param {*} length 所创建的数组缓冲区的大小，以字节(byte)为单位。  
 * @returns {SharedArrayBuffer} 一个大小指定的新 SharedArrayBuffer 对象。其内容被初始化为 0。
 */
new SharedArrayBuffer(length)
```

Atomics 对象提供了一组静态方法用来对 SharedArrayBuffer 对象进行原子操作，这些原子操作属于 Atomics 模块。Atomics 不是构造函数，因此不能使用 new 操作符调用，也不能将其当作函数直接调用。Atomics 的所有属性和方法都是静态的（与 Math 对象一样）。

| 方法名 | 说明 |
| :--- | :--- |
| Atomics.add\(\) | 将指定位置上的数组元素与给定的值相加，并返回相加前该元素的值。 |
| Atomics.and\(\) | 将指定位置上的数组元素与给定的值相与，并返回与操作前该元素的值。 |
| Atomics.compareExchange\(\) | 如果数组中指定的元素与给定的值相等，则将其更新为新的值，并返回该元素原先的值。 |
| Atomics.exchange\(\) | 将数组中指定的元素更新为给定的值，并返回该元素更新前的值。 |
| Atomics.load\(\) | 返回数组中指定元素的值。 |
| Atomics.or\(\) | 将指定位置上的数组元素与给定的值相或，并返回或操作前该元素的值。 |
| Atomics.store\(\) | 将数组中指定的元素设置为给定的值，并返回该值。 |
| Atomics.sub\(\) | 将指定位置上的数组元素与给定的值相减，并返回相减前该元素的值。 |
| Atomics.xor\(\) | 将指定位置上的数组元素与给定的值相异或，并返回异或操作前该元素的值。 |
| Atomics.wait\(\) | 检测数组中某个指定位置上的值是否仍然是给定值，是则保持挂起直到被唤醒或超时。返回值为 "ok"、"not-equal" 或 "time-out"。调用时，如果当前线程不允许阻塞，则会抛出异常（大多数浏览器都不允许在主线程中调用 wait\(\)）。 |
| Atomics.wake\(\) | 唤醒等待队列中正在数组指定位置的元素上等待的线程。返回值为成功唤醒的线程数量。 |
| Atomics.isLockFree\(size\) | 可以用来检测当前系统是否支持硬件级的原子操作。对于指定大小的数组，如果当前系统支持硬件级的原子操作，则返回 true；否则就意味着对于该数组，Atomics 对象中的各原子操作都只能用锁来实现。此静态方法面向的是技术专家。 |


