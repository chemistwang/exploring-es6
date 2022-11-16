# 关于这本书我们需要知道

本书关于 ECMAScript 6（官方名为 ECMAScript 2015），一个 JavaScript 的新版本。

## 适用群体：JavaScript 程序员

理解这本书之前，你应该已经了解了JavaScript。如果还没有的话，我另一本 Speaking JavaScript 是免费在线的，它向程序员讲解了所有的JavaScript （包括 ECMAScript 5）。

## 为什么我要读这本书

* 自己决定深度：本书深入介绍了 ECMAScript 6，不过结构合理，你可以自主快速获得概述。
* 不仅仅是什么，还有为什么：本书不仅告诉你 ES6 如何运作，同时也会告诉你它为什么这么运作。
* 彻底研究：为了搞明白 ES6，我参考了很多资料：
  * 语言规范（本书中你可以偶尔发现链接）
  * ES 讨论的邮件列表
  * TC39 的会议记录
  * 科学论文
  * 在其他语言中能给予 ES6 特性灵感的文档
  * 更多

## 如何读这本书

本书从三细节层次覆盖 ES6：

* 快速开始：从 ES6 核心特性 开始。另外，几乎所有章节都会提供一小段概述。最后一章会把这些概述放在一个单独的位置。
* 扎实的基础：每一章都会从要点开始，然后逐步深入。标题可以很好的提供什么时候停止阅读的建议，但是我会时不时的在侧边栏标注 w.r.t，用来表示一些重要的东西。
* 深入了解：把一章的所有内容，包括深入的部分都读一遍。\


其他需要了解的：

* 建议：我偶尔会推荐简单的规则。多数是作为指南，在你不知道或者回忆不起俩所有细节的时候稳妥一点。相比优雅的编写风格，我更偏向主流风格，毕竟多数代码不是无中生有。但我也会提供足够的信息可以让你自己做出决定。
* 论坛：Exploring ES6 的主页中会有一个论坛的链接，可以讨论关于本书的问题和想法。
* 勘误表（错别字、错误等）：在 Exploring ES6 主页中，会有提交勘误表单的链接和已经提交过错误的列表。

## 本书来源

早在 ES6 特性实现之前，我就着手写这本书，为此参考了大量的研究。主要对来源有：

* ES 讨论的邮件列表
* TC39 会议记录
* ECMAScript 语言规范
* 旧版 ECMAScript Harmony 维基百科
* 科学论文（例如 有篇是关于 ES6 Proxy）和网络上的其他资料
* 寻求填补剩余漏洞（在本书中承认回答的那些人）

## 词汇表

### 严格模式 vs 普通模式

ECMAScript 5 引入了语言模式：严格模式改变语义、执行更多的检查，抛出更多异常，让 JavaScript 变得更为简洁。Consult Sect。Speaking JavaScript 这本书对严格模式会有更多信息。传统/默认模式也被称为非严格模式/普通模式。

严格模式可以通过下面这行代码切换（在ES5之前的 ECMAScript 版本不用有任何变化）：

```javascript
'use strict';
```

如果把它放在文件开头，那么该文件的所有代码都会是严格模式。如果是函数的第一行， 那么只有这个函数是严格模式。

用这种指令切换至严格模式其实并不友好，这也是为什么严格模式在 ES5 中并不流行的原因之一。但是，ES6 模块和类默认是严格模式。鉴于大多数 ES6 代码都存在于模块中，实际上严格模式变成了ES6的默认设置。

### 协议

_协议_ 这个术语在计算机中有很多含义。根据语言上下文和API的设计，我会这么定义它：

> 协议定义了使用它们的接口（方法和/或函数的签名）和规则。

这个理念表明了一个服务是如何被执行的。这个时候所有人都可以执行这个服务并且请求它，这样就能保证相互之间很好的合作。

注意这里的定义不同于将协议视为接口（例如，Objective C那样），因为这个定义包含了规则。

### 接收者（方法调用的）

给定一个方法调用 `obj.m(...)`，`obj` 是方法调用的接收者，并且在方法内部可以通过 `this` 访问。

### 函数（或方法）签名

一个函数的（类型）签名描述了该方法如何调用，它的输入和输出是什么。在本书中，我用了 Microsoft 的 TypeScript 和 Facebook 的 Flow 语法。一个签名示例：

```javascript
parseInt(string: string,radix?: number): number
```

可以看到，`parseInt()` 期望一个字符串和一个数字，并且返回一个数字。如果一个参数类型很明确，我通常会省略类型声明。

### 内部插槽

ES6 的语言规范使用内部插槽存储内部数据。在规范中，可以通过访问方括号属性一样访问内部插槽：

```javascript
O.[[GetPrototypeOf]]()
```

它们有两点跟属性不同：

* 它们不通过 get 操作符读取，也不通过 set 操作符写入。
* 只在规范所知，无法从 JavaScript 中访问。比如：一个对象和它的原型之间的是通过内部插槽 `[[Prototype]]`关联。该插槽的值无法通过 JavaScript 直接读取，不过可以用 Object.getPrototypeOf() 去获取。

内部插槽是如何存储的目前没被确定。有些甚至不存在于 JavaScript 的实现中。

### 绑定和环境

ECMAScript 规范中使用了一种称为环境的数据结构，用来存储一个作用域中的变量。环境是基于一个字典，也就是一组组变量名和值的映射关系。绑定则是一个变量环境，存储空间的入口。

### 破坏性操作

破坏性操作（方法，函数）会修改参数或者它们的接收者。例如：push() 会改变 arr：

```javascript
> const arr = ['a', 'b'];
> arr.push('c')
3
> arr
['a', 'b', 'c']
```

相反，concat() 会创建一个新数组，而且不会改变 arr：

```javascript
> const arr = ['a', 'b'];
> arr.concat(['c'])
['a', 'b', 'c']
> arr
['a', 'b']
```

## 约定

### 定义类

一个名为 c 的类，它的 API 通常被如下记录：

* c 构造函数
* c 的静态方法
* C.prototype 方法

### 大小写

英语中，我会这么约定 JavaScript 的大小写：

* 原始类型不大写：boolean，number，symbol，string。我这么做其中一个原因是 TypeScript 和 Flow 会将它们区别开：
  * String 类型：它的成员是对象，String的实例
  * string 类型：它的成员是基础类型，字符串
* Map 这个数据结构大写。原因：区别于 Array 的方法 map()
* Set 这个数据结构大写。原因：区别于动词 set.
* Array 和 Promise 大写。原因：容易和它们的英语单词混淆
* 不大写（暂时）：object，generator，proxy

## Github 上的示例代码

本书中展示的代码放在了 Github 上的一些仓库里：

* [async-examples](https://github.com/rauschma/async-examples)
* [babel-on-node](https://github.com/rauschma/babel-on-node)
* [demo\_promise](https://github.com/rauschma/demo\_promise)
* [generator-examples](https://github.com/rauschma/generator-examples)
* [node-es6-demo](https://github.com/rauschma/node-es6-demo)
* [promise-examples](https://github.com/rauschma/promise-examples)
* [webpack-es6-demo](https://github.com/rauschma/webpack-es6-demo)

## 侧边栏

侧边栏是标有图表的文本框。它们是正常文本的补充。

## 脚注

有时，我会用脚注引用（公开可用的）外部材料。这俩用前缀是方括号的记号来标注：

* \[Spec] 引用的是 ES6 HTML 版本规范中的内容
* \[Speaking JS] 引用的是 Speaking JavaScript HTML 版本中的内容
