# 前言

边界情况！作为一名 ES6 规范的项目编辑，我的工作全是关于边界。就像大多数软件一样，编程语言特性的设计通常由特定用例驱动。但程序员经常用奇特的方式用语言的特性，通常远远超过用例范围。还有一点，没有语言的功能是独立的。它们都会和其他功能有交互。这些意想不到的用法和功能交互都属于极端情况。

比如，consider a function that has a parameter default value initialization expression that uses the eval function to first declare a local variable that has the same name as a local variable declared in the function body and then returns, as the parameter value, an arrow function that references that name。如果在函数体内访问这个参数并调用了箭头函数，会发生什么呢？访问的是哪个变量？是否需要检测并报告一个错误？在设计 ES6 的时候，正是这些边界情况让我彻夜难眠。

一个不错的语言必须考虑到这样的边缘情况。广泛流行的语言规范都会有多种多样的实现方案，它们必须把这些边缘情况确定下来。否则，语言的不同实现会以不同的方式处理这些情况，那么程序就不会运行一致。

在你处理异常情况和边界情况的时候，如果你真的想理解 ES6，你必须明白每个特性是如何实现的。Axel Rauschmayer 这本 Exploring ES6 和其他书不一样的是，它更关注 ECMAScript 的内部实现原理。它不仅仅停留在你所知道的常见用例。而是深挖语义，在必要的时候，在边界情况里摸爬滚打。它解释了为什么这些特性这么实现，在实际代码中怎么用。吸收理解了本书的内容，你离 ES6 专家也就不远了。

Allen Wirfs-Brock

ECMAScript 2015 (ES6) 规范编辑



