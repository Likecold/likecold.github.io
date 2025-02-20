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

### 1.4 ES 9 新特性

| 新特性 | 中文说明 |
| :--- | :--- |
| Asynchronous Iteration | 异步迭代器 |
| `Promise.prototype.finally` |  |
| Lifting template literal restriction | 重新修订了字面量的转义 |
| Rest/Spread Properties | Rest/Spread 属性 |
| `s` \(dotAll\) flag for regular expressions | 正则表达式dotAll模式 |
| RegExp named capture groups | 正则表达式命名捕获组 |
| RegExp Lookbehind Assertions | 正则表达式反向断言 |
| RegExp Unicode Property Escapes | 正则表达式 Unicode 转义 |

#### 异步迭代器

在`async`/`await`的某些时刻，你可能尝试在同步循环中调用异步函数。例如：

```javascript
async function func(array) {
  for (let i of array) {
    await someFunc(i);
  }
}
```

这段代码不会达到预期目的，下面这段同样也不会：

```javascript
async function func(array) {
  array.forEach(async i => {
    await someFunc(i);
  });
}
```

上面这段代码中，循环本身依旧保持同步，并在内部异步函数之前全部调用完成。

引入异步迭代器后，就像常规迭代器，除了`next()`方法返回一个Promise。因此`await`可以和`for...of`循环一起使用，以串行的方式运行异步操作。

```javascript
async function func(array) {
  for await (let i of array) {//异步迭代
    someFunc(i);
  }
}
```

更多详细论述见“壹.2.12”。

#### Promise.prototype.finally

一个Promise调用链要么成功到达最后一个`.then()`，要么失败触发`.catch()`。在某些情况下，你想要在无论Promise运行成功还是失败，运行相同的代码，例如清除数组、删除对话、关闭数据库连接等，`.finally()`允许实现这样的目的。

```javascript
function func() {
  promiseFunc() //返回一个Promise对象
    .then(() => {})
    .then(() => {})
    .catch(err => {
      console.log(err);
    })
    .finally(() => {
      //无论promiseFunc()运行成功还是失败，这里的代码会被调用到
    });
}
```

更多详细论述见“壹.2.12”。

#### 重新修订了字面量的转义

ES9 之前，`\u`表示 unicode 转义，`\x`表示十六进制转义，`\`后跟一个数字表示八进制转义，这使得创建特定的字符串变得不可能，例如Windows文件路径`C:\uuu\xxx\111`。

要取消转义序列的语法限制，可在模板字符串之前使用标记函数`String.raw`。

```javascript
let s = `\u{54}` //会转义成unicode "T"
console.log(s);//>> T

let str = String.raw`\u{54}`; //不会被转义
console.log(str);//>> \u{54}
```

#### Rest / Spread

这个就是我们通常所说的三个点`...`，这项特性在ES6中已经引入，但是ES6中的作用对象仅限于数组。在ES9中，为对象提供了像数组一样的rest参数和扩展运算符：

```javascript
const obj = {
  a: 1,
  b: 2,
  c: 3
};
const { a, ...param } = obj; //这里...是rest
console.log(a); //>> 1
console.log(param); //>> {b: 2, c: 3}

function foo({ a, ...param }) {//这里...还是rest
  console.log(a); //>> 1
  console.log(param); //>> {b: 2, c: 3}
}

const param = { b: 2, c: 3 };
foo({ a: 1, ...param });  //此处...为spread
```

#### 正则表达式dotAll模式

正则表达式中点`.`匹配除回车外的任何单字符，标记`s`改变这种行为，允许匹配回车换行。

```javascript
/hello.world/.test('hello\nworld');  // false
/hello.world/s.test('hello\nworld'); // true
console.log(/hello.world/s.test(`hello
world`))   //>> true
```

#### 正则表达式命名捕获组

Javascript正则表达式中使用`exec()`匹配后，能够返回一个包含匹配字符串的类数组对象。

```javascript
const reDate = /(\d{4})-(\d{2})-(\d{2})/,
  match = reDate.exec("2018-08-06");
console.log(match);//>> [2018-08-06, 2018, 08, 06]

//这样就可以直接用索引来获取年月日：
let year = match[1]; //>> 2018
let month = match[2]; //>> 08
let day = match[3]; //>> 06
```

返回数组的第0项为与正则表达式相匹配的文本，第 1 项是与 `reDate` 的第 1 个分组`\d{4}`相匹配的文本（如果有的话），第 2 项是与 `reDate` 的第 2 个分组`\d{2}`相匹配的文本（如果有的话），以此类推。正则表达式的组以`()`包起来。

上面的案例，若是日期格式变成`月日年`，那么改变正则表达式的结构后，还有可能会改变变量的赋值部分的代码。如下示例：

```javascript
const reDate = /(\d{2})-(\d{2})-(\d{4})/,//表达式结构变化了
  match = reDate.exec("08-06-2018");
console.log(match);//>> [08-06-2018, 08, 06, 2018]

//此时年月日的赋值代码也要改了,改的地方真多啊！怎么办？
let year = match[3]; //>> 2018
let month = match[1]; //>> 08
let day = match[2]; //>> 06
```

可以发现上面的写法改的地方太多了，有没有办法少改点代码省省事呢？有！ ES9 允许使用符号`?<name>`来命名**捕获组**（也即“匹配到的组”），示例如下：

```javascript
const reDate = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/,
  match = reDate.exec("2018-08-06");
console.log(match);
//>> [2018-08-06, 08, 06, 2018, groups: {day: 06, month: 08, year: 2018}]

//此时用groups对象来获取年月日，无论正则表达式怎么变换，这下面三行不用改了，省事！
let year = match.groups.year; //>> 2018
let month = match.groups.month; //>> 08
let day = match.groups.day; //>> 06
```

命名捕获组的写法相当于是把每个捕获组都定义了一个名字，然后存储到返回值的`groups`属性中。

#### 正则表达式后行断言

先看看正则表达式**先行断言**示例：

```javascript
const re1 = /\D(\d+)/,
    re2 = /\D(?=\d+)/,//"?="是正向先行断言
    match1 = re1.exec("$123.45"),
    match2 = re2.exec("$123.45");
console.log(match1[0]); //>> $123
console.log(match2[0]); //>> $
```

> **\(?=pattern\)** 零宽正向先行断言\(zero-width positive lookahead assertion\)  
> 代表字符串中的一个位置，**紧接该位置之后**的字符序列**能够匹配**pattern；
>
> \(**?!pattern\)** 零宽负向先行断言\(zero-width negative lookahead assertion\)  
> 代表字符串中的一个位置，**紧接该位置之后**的字符序列**不能匹配**pattern；
>
> **\(?&lt;=pattern\)** 零宽正向后行断言\(zero-width positive lookbehind assertion\)  
> 代表字符串中的一个位置，**紧接该位置之前**的字符序列**能够匹配**pattern；
>
> **\(?&lt;!pattern\)** 零宽负向后行断言\(zero-width negative lookbehind assertion\)  
> 代表字符串中的一个位置，**紧接该位置之前**的字符序列**不能匹配**pattern。

在ES9中，可以允许使用`?<=`进行**正向后行断言**，可以获取货币的价格而忽略货币符号。

```javascript
const re= /(?<=\D)[\d\.]+/,
    match = re.exec("$123.45");
console.log(match[0]); //>> 123.45
```

上面的正向后行断言，也就是说`\D`这个条件必须满足，但`\D`匹配的字符不会作为结果输出（因为**先行/后行断言**其实只是匹配了一个位置）。如果是下面这样：

```javascript
const re= /(?<=\D)[\d\.]+/,
    match1 = re.exec("123.45"),
    match2 = re.exec("12345");
console.log(match1 && match1[0]); //>> 45
console.log(match2 && match1[0]); //>> null
```

可以看到`match1`匹配到的是`45`,这是由于在`.`前面没有任何符合`\D`的匹配内容，它会一直找到符合`\D`的内容，也就是`.`然后返回后面的内容。而`match2`若是没有满足前面肯定反向断言的条件的话，则结果返回`null`。

#### 正则表达式 Unicode 转义

ES9 之前，在正则表达式中本地访问 Unicode 字符属性是不被允许的。ES9 添加了 Unicode 属性转义，形式为`\p{...}`和`\P{...}`，在正则表达式中使用标记 `u` \(unicode\) 设置，在`\p`的`{...}`内，可用键值对的方式设置需要匹配的属性而非具体内容。

```javascript
const regex = /\p{Script=Greek}/u;//Greek为希腊语的意思
console.log(regex.test('a')); //>> flase
console.log(regex.test('Σ')); //>> true
```

### 1.5 ES 10 新特性

| 新特性 | 中文说明 |
| :--- | :--- |
| Optional `catch` binding | 可选的 catch 变量绑定 |
| JSON superset | JSON超集 |
| `Symbol.prototype.description` | Symbol 对象的 description 属性 |
| `Function.prototype.toString` revision | 修订`Function.prototype.toString` |
| `Object.fromEntries` |  |
| Well-formed `JSON.stringify` | 更加友好的JSON.stringify |
| `String.prototype.{trimStart,trimEnd}` |  |
| `Array.prototype.{flat,flatMap}` |  |

#### 可选的 catch 变量绑定

在 ES10 之前，我们必须通过语法为 catch 子句绑定异常变量，无论是否有必要。很多时候 catch 块是多余的，而 ES10 使我们能够简单的把变量省略掉。

```javascript
//之前是
try {} catch(e) {}

//ES10之后可以写成，
try {} catch {}//省掉了变量e
```

#### JSON超集

什么是 JSON 超集？简单来说就是 JSON 是 ECMAScript 的子集，也就是说让 ECMAScript 兼容 JSON 的内容所支持的全部文本。

ECMAScript 在标准 [JSON.parse](https://tc39.es/ecma262/#sec-json.parse) 部分阐明了 JSON 确为其一个子集，但由于 JSON 的内容可以正常包含 `U+2028` 行分隔符与 `U+2029` 段落分隔符，而 ECMAScript 却不行，所以，该草案旨在解决这一问题。在这之前，如果你使用 `JSON.parse()` 执行带如上特殊字符的字符串时，只会收到 `SyntaxError` 的错误提示。该草案同样是向后兼容的，其对用户唯一的影响是保持原样，即在暂不支持特殊字符解析的运行环境中保持报错 `SyntaxError` 。

#### Symbol 对象的 description 属性

ES10 中为 Symbol 对象添加了只读属性 `description` ，该对象返回包含 Symbol 描述的字符串。在创建Symbol时向其添加`description` \(描述\)，能够直接访问`description` ，对调试是很有用的。

```javascript
let sym = Symbol('foo');//添加的描述内容为“foo”
console.log(sym.description);//>> foo

sym = Symbol();
console.log(sym.description);//>> undefined

//和 Symbol() 不同的是，用 Symbol.for() 方法创建的的 symbol 会被放入一个全局 
//symbol 注册表中。Symbol.for() 并不是每次都会创建一个新的 symbol，它会首先检
//查给定的 key 是否已经在注册表中了。假如是，则会直接返回上次存储的那个。否则，它
//会再新建一个。
sym = Symbol.for('bar');
console.log(sym.description);//>> bar
```

#### 修订Function.prototype.toString

函数原型上的方法`toString()`现在返回精确字符，包括空格和注释。

```javascript
function /* comment */ foo /* another comment */() {}

//ES10之前不会打印注释部分
console.log(foo.toString()); //>> function foo(){}

//ES10里，会把注释一同打印
console.log(foo.toString()); //>> function /* comment */ foo /* another comment */ (){}

//注意：
//箭头函数是个例外
const bar /* comment */ = /* another comment */ () => {};
console.log(bar.toString()); //>> () => {}
```

#### Object.fromEntries

在 JavaScript 操作中，数据在各种数据结构之间的转换都是很容易的，比如 Map 到数组、Map 到 Set、对象到 Map 等等。

```javascript
let map = new Map().set('foo', true).set('bar', false);
let arr = Array.from(map);
let set = new Set(map.values());

let obj = { foo: true, bar: false };
//下一句 Object.entries() 方法返回给定对象 obj 自身可枚举属性的键值对数组,
//形如：[["foo",true],["bar",false]]
let newMap = new Map(Object.entries(obj));
```

但是如果我们需要将一个键值对列表转换为对象，就要写点费劲的代码了。

```javascript
let map = new Map().set("foo", true).set("bar", false);
let obj = Array.from(map).reduce((acc, [key, val]) => {
  return Object.assign(acc, {
    [key]: val
  });
}, {});
```

该特性的目的在于为对象添加一个新的静态方法 `Object.fromEntries`，用于将符合键值对的列表（例如 Map、数组等）转换为一个对象。上一块的代码中的转换逻辑，现在我们只需要一行代码即可搞定。

```javascript
const map = new Map().set("foo", true).set("bar", false);
let obj = Object.fromEntries(map);
```

#### 更加友好的 JSON.stringify

ES10 之前，当你使用 `JSON.stringify()` 处理无法用 UTF-8 编码表示的字符时（U+D800 至 U+DFFF），返回的结果会是一个乱码 Unicode 字符“�”。该特性提出用`JSON.stringify()`来安全的表示这些不正常的UTF-8字符。

```javascript
let r;
r = JSON.stringify("❤"); //正常的UTF-8字符原样输出
console.log(r); //>> "❤"

r = JSON.stringify('\u2764'); //正常的UTF-8字符编码，输出解码之后的模样
console.log(r); //>> "❤"

r = JSON.stringify("\uDF06\uD834"); //不正常的UTF-8字符编码，则以unicode形式输出
console.log(r); //>> "\udf06\ud834"

r = JSON.stringify("\uDEAD"); //不正常的UTF-8字符编码，则以unicode形式输出
console.log(r); //>> "\udead"
```

#### String.prototype.{trimStart,trimEnd}

新增了String的`trimStart()`方法和`trimEnd()`方法，这两个方法很好理解，分别去除字符串首、尾的空白字符，就不举例占篇幅了。

#### Array.prototype.{flat,flatMap}

这个特性新创造了两个方法，其中：

* `Array.prototype.flat` 数组的所有项会以指定的维度降维（扁平化），然后组成新数组返回；
* `Array.prototype.flatMap` 首先会执行一次`map()`方法，然后再通过类似`flat()`方法**扁平化**数组。它等同于执行完 `map()` 后再执行一次 `flat()` 方法，所以当你执行 `map()` 返回的结果如果是个数组，然后又要将其扁平化时，这个方法会显得方便。

来看几个例子解释一下，首先 `flat()` 方法支持多维数组的扁平化，其中`Infinity`可以将多维数组压扁成一维数组。

```javascript
let r;
r = ["1", ["8", ["9", ["1"]]]].flat();//4维数组，默认降维1，变成3维数组
console.log(r); //>> [ '1', '8', [ '9', ['1'] ] ]

r = ["1", ["8", ["9", ["1"]]]].flat(2); //4维数组，降维2，变成2维数组
console.log(r); //>> [ '1', '8', '9', ['1'] ]

r = ["1", ["8", ["9", ["1"]]]].flat(Infinity);//4维数组，最多变成1维数组
console.log(r); //>> [ '1', '8', '9', '1' ]
```

接着来看看`flatMap()`

```javascript
let r;
r = ["I love", "coffe 1891"].map(item => item.split(" "));
console.log(r); //>> [ [ 'I', 'love' ], [ 'coffe', '1891' ] ]

r = ["I love", "coffe 1891"].flatMap(item => item.split(" "));
console.log(r); //>>[ 'I', 'love', 'coffe', '1891' ]
```

### 1.6 ES 2020 \(ES11\) 新特性

因为太“新”，现在若想使用ES11新特性，则需要安装babel插件

```javascript
plugins: [
    "@babel/plugin-proposal-nullish-coalescing-operator",
    "@babel/plugin-proposal-optional-chaining",
    "@babel/plugin-proposal-class-properties",
    "@babel/plugin-proposal-private-methods",
    "@babel/plugin-syntax-bigint"
]
```

| 新特性 | 中文说明 |
| :--- | :--- |
| Optional Chaining | 可选链操作符 |
| Nullish coalescing Operator | 空位合并操作符 |
| `String.prototype.matchAll` |  |
| `import()` | import\(\)函数 |
| `Promise.allSettled` |  |
| Bigint |  |
| globalThis | 全局this |

#### Optional Chaining

**可选链操作符** `?` 可让我们在查询具有多个层级的对象时，不再需要进行冗余的各种前置校验。

以前要读取一个对象的属性值，需要写一些防御性的前置校验代码，比如：

```javascript
let second = obj && obj.first && obj.first.second;
```

在访问 obj.first.second 之前，要先确认 obj 和 obj.first 的值非 null\(且不是 undefined\)。有了可选链式操作符，则可以大量简化类似繁琐的前置校验操作，而且更安全：

```javascript
let second = obj?.first?.second;
```

如果 `obj` 或`obj.first`是`null`/`undefined`，表达式将会直接返回`undefined`。

#### Nullish coalescing Operator\(空值处理\)

以前对于如下的情况：

```javascript
let v = a || "some value";

let z = a ? a : "some value";
```

如果 a 的值是 `0` 、 空字符串`''` 、`false`等可能有意义的值，但是在上面的表达式中被认为是假值，因此v 和 z 也会被赋值为 `some value`。也即：

```javascript
let a = 0; // 0、''、false可能是有意义的
let v = a || "some value";
console.log(v); //>> some value
```

为解决这种问题，诞生了`??` ，如果表达式在`??`的左侧值为`undefined`或`null`，就返回右侧默认的值。

```javascript
let a = 0; 
let v = a ?? "some value";
console.log(v); //>> 0

let b = null; 
let z = b ?? "some value";
console.log(z); //>> some value
```

#### String.prototype.matchAll

`matchAll()` 方法返回一个包含所有匹配正则表达式及分组捕获结果的迭代器（iterator）。 在 `matchAll` 出现之前，通过在循环中调用`Regexp.exec`来获取所有匹配项信息（`Regexp`需使用`/g`标志）：

```javascript
const regexp = RegExp('foo*','g');
const str = 'coffe football, foosball';

while ((matches = regexp.exec(str)) !== null) {
  console.log(`找到 ${matches[0]}，下一轮循环从位置 ${regexp.lastIndex} 开始`);
  //>> 找到 foo，下一轮循环从位置 9 开始
  //>> 找到 foo，下一轮循环从位置 19 开始
}
```

如果使用`matchAll` ，就可以不必使用`while`循环加`exec`方式（且正则表达式需使用`/g`标志）。使用`matchAll` 会得到一个迭代器的返回值，配合 `for...of`，`array spread`，`Array.from()` 可以更方便实现功能。

```javascript
const regexp = RegExp('foo*','g'); 
const str = 'coffe football, foosball';
let matches = str.matchAll(regexp);

for (const match of matches) {
  console.log(match);
}
//>> [ "foo" ]
//>> [ "foo" ]

//注意：
//matches的迭代器在for..of之后已经被消耗掉了，
//需要再次调用matchAll创建一个新的迭代器
matches = str.matchAll(regexp);

let arr = Array.from(matches, m => m[0]);
console.log(arr);
//>> [ "foo", "foo" ]
```

#### import\(\)函数

这个特性为JavaScript添加了一个类函数（function-like）的`import()`功能，以便可以像函数传参那样传入参数实现**动态**（没错，`import`是静态引用的）引用模块（module）。下面有个单页应用简单示例，演示了用`import()`开启懒加载。

```markup
<!DOCTYPE html>
<nav>
  <a href="books.html" data-entry-module="books">书籍</a>
  <a href="movies.html" data-entry-module="movies">电影</a>
  <a href="video-games.html" data-entry-module="video-games">电视游戏</a>
</nav>

<main>内容将会加载到这里！</main>

<script>
  const main = document.querySelector("main");
  for (const link of document.querySelectorAll("nav > a")) {
    link.addEventListener("click", e => {
      e.preventDefault();

      import(`./section-modules/${link.dataset.entryModule}.js`)//动态引用
        .then(module => {//加载模块成功以后，该模块会当作then方法的参数
          module.loadPageInto(main);
        })
        .catch(err => {//捕捉异常
          main.textContent = err.message;
        });
    });
  }
</script>
```

请注意`import()`和`import`的区别：

* import\(\) 可以用在script脚本区，不止是模块内；
* 如果在模块内使用import\(\)，它可以在任何地方任何级别执行，而不是被提升到顶级（优先执行）；
* import\(\) 是运行时执行，也即什么时候运行到这句，就会加载参数指定的模块；参数也可以是动态可变的，不止是静态参数；
* import\(\) 不建立可静态分析的依赖关系（静态分析的情况下可以做很多优化），但是，在一些比较简单的情况下，比如`import（“/foo.js”）`中，实现仍然可以执行静态分析优化。

如果模块采用`default`的形式对外暴露接口，则可用`default`属性直接获得。

```javascript
import('./module.js')
.then(module => {
  console.log(module.default);//直接通过default属性获得模块暴露的接口
});
```

#### Promise.allSettled

为什么要有`Promise.allSettled()`？

举例说明，比如各位用户在页面上面同时填了3个独立的表单，这三个表单分三个接口提交到后端，三个接口独立，没有顺序依赖，这个时候我们需要等到请求全部完成后给与用户提示表单提交的情况。

在多个`promise`同时进行时咱们很快会想到使用`Promise.all`来进行包装，但是由于`Promise.all`的一票否决的特性，三个提交中若前面任意一个提交失败，则后面的表单也不会进行提交了，这就与咱们需求不符合。

`Promise.allSettled`跟`Promise.all`类似，其参数接受一个`Promise`的数组，返回一个新的`Promise`，唯一的不同在于，其没有一票否决的特性，也就是说当`Promise`全部处理完成后我们可以拿到每个`Promise`的状态，而不管其是否处理成功。

```javascript
Promise.allSettled([Promise.resolve("coffe"), Promise.reject("1891")]).then(
  arr => {
    console.log(arr); //>> [ { status: "fulfilled", value: "coffe"},
                      //>>   { status: "rejected", reason: "1891" } ]
  }
);
```

#### Bigint

JavasSript 在数学计算领域很糟糕的原因之一是它只能安全的表示`-(2^53-1)`至 `2^53-1` 范的值，即`Number.MIN_SAFE_INTEGER` 至`Number.MAX_SAFE_INTEGER`，超出这个范围的整数计算或者表示会丢失精度。

```javascript
var num = Number.MAX_SAFE_INTEGER;  // >> 9007199254740991

num = num + 1; // >> 9007199254740992

// 再次加 +1 后无法正常运算
num = num + 1; // >> 9007199254740992

// 两个不同的值，却返回了true
9007199254740992 === 9007199254740993  // >> true
```

于是 BigInt 诞生了，**它是JavaScript的第7个原始类型**，可安全地进行大数整型计算。 你可以在BigInt上使用与普通数字相同的运算符，例如 +, -, /, \*, %等等。

创建 BigInt 类型的值也非常简单，只需要在数字后面加上 `n` 即可。例如，123 变为 123n。也可以使用全局方法 BigInt\(value\) 转化，入参 value 为数字或数字字符串。

```javascript
const aNumber = 1891;
const aBigInt = BigInt(aNumber);
aBigInt === 1891n // true
typeof aBigInt === 'bigint' // true
typeof 1891 // "number"
typeof 1891n // "bigint"
```

只要在数字末尾加上 n，就可以正确计算大数：

```javascript
1234567890123456789n * 123n;
// -> 151851850485185185047n
```

最后要注意，不能将 BigInt与Number混合使用。比较Number和 BigInt是可以的，但是不能把它们相加。

```javascript
1n < 2 // true

1n + 2 // Uncaught TypeError: Cannot mix BigInt and other types, use explicit conversions
```

#### globalThis

globalThis 是一个全新的标准方法用来获取全局 this 。之前开发者会通过如下的一些方法获取：

* 全局变量 window：是一个经典的获取全局对象的方法。但是它在 Node.js 和 Web Workers 中并不能使用
* 全局变量 self：通常只在 Web Workers 和浏览器中生效。但是它不支持 Node.js。一些人会通过判断 self 是否存在识别代码是否运行在 Web Workers 和浏览器中
* 全局变量 global：只在 Node.js 中生效

过去获取全局对象，可通过一个全局函数：

```javascript
// ES10之前的解决方案
const getGlobal = function(){
  if(typeof self !== 'undefined') return self
  if(typeof window !== 'undefined') return window
  if(typeof global !== 'undefined') return global
  throw new Error('unable to locate global object')
}

// ES10内置
globalThis.Array(0,1,2) // [0,1,2]

// 定义一个全局对象v = { value:true } ,ES10用如下方式定义
globalThis.v = { value:true }
```

而 **globalThis 目的就是提供一种标准化方式访问全局对象**，有了 globalThis 后，你可以在任意上下文，任意时刻都能获取到全局对象。如果您在浏览器上，globalThis将为window，如果您在Node上，globalThis则将为global。因此，不再需要考虑不同的环境问题。

```javascript
// worker
globalThis === self
// node
globalThis === global
// browser
globalThis === window
```

### 1.7 本篇结语

很显然ECMAScript接下来会持续不断地更新，按TC39的计划是每年都会发一个新版本。虽然节奏很快，但是我们完全没必要担心跟不上节奏。除了ES6这个史无前例的版本带来了超大量的新特性外，之后每年发的版本都仅仅带有少量的增量更新，你只需要花45分钟就能搞明白这一年更新的特性。保持一颗好奇的心，你会不断进步，变得更强！

### 参考文献

[ES2020新特性](https://juejin.im/post/6844904080955932679#heading-6)

