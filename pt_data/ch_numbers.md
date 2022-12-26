# 5. number 和 Math 新特性

### 5.1 概述

#### 5.1.1 新的整数语法

现在可以用二进制和八进制制定整数：

```javascript
> 0xFF // ES5: hexadecimal
255
> 0b11 // ES6: binary
3
> 0o10 // ES6: octal
8
```

#### 5.1.2 新的 Number 属性

全局对象 Number 获得了一些新属性：

* Number.EPSILON 用于比较 a tolerance for rounding errors 的浮点数。
*   Number.isInteger(num) 检查 num 是否是整数（没有小数的数字）：\\

    ```
      > Number.isInteger(1.05)
      false
      > Number.isInteger(1)
      true

      > Number.isInteger(-3.1)
      false
      > Number.isInteger(-3)
      true
    ```
* 用于确定一个 JavaScript 整数是否安全的方法和常数（在没有精度损失带符号的 53 位范围内）：
  * `Number.isSafeInteger(number)`
  * `Number.MIN_SAFE_INTEGER`
  * `Number.MAX_SAFE_INTEGER`
*   Number.isNaN(num) 检查 num 是否为 NaN。与全局函数 isNaN() 相比，它不会将其参数强制转换为数字，因此对于非数字来说更安全：

    ```
    > isNaN('???')
    true
    > Number.isNaN('???')
    false
    ```
* Number 的三个附加方法大多等同于具有相同名称的全局函数：Number.isFinite、Number.parseFloat、Number.parseInt。

#### 5.1.3 新的 Math 方法

全局对象 Math 具有用于数值、三角和位运算的新方法。让我们看四个例子。

Math.sign() 返回数字的符号：

```
> Math.sign(-8)
-1
> Math.sign(0)
0
> Math.sign(3)
1
```

Math.trunc() 删除数字的小数部分：

```
> Math.trunc(3.1)
3
> Math.trunc(3.9)
3
> Math.trunc(-3.1)
-3
> Math.trunc(-3.9)
-3
```

Math.log10() 计算以 10 为底的对数：

```
> Math.log10(100)
2
```

Math.hypot() 计算其参数平方和的平方根（毕达哥拉斯定理）：

```
> Math.hypot(3, 4)
5   
```

### 5.2 新的整数

ECMAScript5 已经有了16进制整数：

```
> 0x9
9
> 0xA
10
> 0x10
16
> 0xFF
255
```

ECMAScript6 带来了两种新的整数：

*   二进制开头的 0b 或者 0B：

    ```
     > 0b11
      3
     > 0b100
      4
    ```
*   八进制开头的 0o 或者 0O（这是一个0后面跟了大写的O；第一个变体更安全）：

    ```
    > 0o7
      7
    > 0o10
      8
    ```

请记住，Number 方法 toString(radix) 可用于查看 以 10 以外为基数表示的数字：

```
> 255..toString(16)
'ff'
> 4..toString(2)
'100'
> 8..toString(8)
'10'
```

（双点是必要的，这样访问属性的点和小数点就不会混淆）

#### 5.2.1 八进制用例：Unix风格的文件权限

在 Node.js 文件系统模块中，一些函数有参数 mode。它的值用于指定文件权限，通过一种从 Unix 继承下来的编码：

* 为3类用户指定权限：
  * User：文件所有者
  * Group：与文件关联的组的成员
  * All：所有人
* 每一类中，可以授予以下权限：
  * r(read)：允许类别用户读取文件
  * w(write)：允许类别用户更改文件
  * x(execute)：允许类别用户运行文件

这意味着权限可以用9位表示（3个类别，每个类别有3个权限）：

|             | User  | Group | All   |
| ----------- | ----- | ----- | ----- |
| Permissions | r,w,x | r,w,x | r,w,x |
| Bit         | 8,7,6 | 5,4,3 | 2,1,0 |

每类的用户权限用 3 位存储：

| Bits | Permissions | Octal digit |
| ---- | ----------- | ----------- |
| 000  | ---         | 0           |
| 001  | --x         | 1           |
| 010  | -w-         | 2           |
| 011  | -wx         | 3           |
| 100  | r--         | 4           |
| 101  | r-x         | 5           |
| 110  | rw-         | 6           |
| 111  | rwx         | 7           |

这说明八进制是所有权限的紧凑表示，你只需要 3 位数字，每个用户类别一位数字。两个例子：

* 755 = 111，101，101：所有者可以修改、读和执行；其他人只能读和执行。
* 640 = 110，100，000：所有者可以修改和读；group 用户只能读；其他人无法访问。

#### 5.2.2 Number.parseInt() 和新的整数

Number.parseInt()（与全局函数 parseInt() 的作用相同）有以下 signature：

```javascript
Number.parseInt(string, radix?)
```

\#### 5.2.2.1 Number.parseInt()：十六进制数字\
Number.parseInt() 为十六进制提供了特殊支持 - 字符串前缀 0x（0X）在以下情况会被移除：

* radix 缺失或者为0。然后 radix 设定为 16。通常，不应该省略 radix。
* radix 为 16

例如：

```
> Number.parseInt('0xFF')
255
> Number.parseInt('0xFF', 0)
255
> Number.parseInt('0xFF', 16)
255
```

其他所有情况下，数字只被解析到第一个非数字：

```
> Number.parseInt('0xFF', 10)
0
> Number.parseInt('0xFF', 17)
0
```

\#### 5.2.2.2 Number.parseInt()：二进制和八进制数字

但是，Number.parseInt() 对二进制或者八进制没有特殊支持！

```
> Number.parseInt('0b111')
0
> Number.parseInt('0b111', 2)
0
> Number.parseInt('111', 2)
7

> Number.parseInt('0o10')
0
> Number.parseInt('0o10', 8)
0
> Number.parseInt('10', 8)
8
```

如果你想解析这种类型，需要用 Number()：

```
> Number('0b111')
7
> Number('0o10')
8
```

Number.parseInt() 适用于有不同基数的数字，只要没有特殊前缀并且提供了 radix：

```
> Number.parseInt('111', 2)
7
> Number.parseInt('10', 8)
8
```

### 5.3 新的静态 Number 属性

本节介绍在 ECMAScript6 中，Number 构造函数的新属性。

#### 5.3.1 之前的全局函数

已经有4个number相关的函数作为全局方法可以用了，并已经添加到 Number 中：isFinite，isNaN，parseFloat 和 parseInt。

它们跟全局方法效果几乎相同，但是 isFinite 和 isNaN 不再将参数强制转化为数字，这对 isNaN 尤其重要。下面小节解释了所有细节。

\#### 5.3.1.1 Number.isFinite(number)

Number.isFinite(number) 确定 number 是否为实际的数字（既不是 Infinity，也不是 -Infinity 和 NaN）：

```
> Number.isFinite(Infinity)
false
> Number.isFinite(-Infinity)
false
> Number.isFinite(NaN)
false
> Number.isFinite(123)
true
```

这个方法的优点是它不会将参数强制转换为数字（全局方法会这么做）：

```
> Number.isFinite('123')
false
> isFinite('123')
true
```

\#### 5.3.1.2 Number.isNaN(number)

Number.isNaN(number) 检查 number 是否是 NaN。

ES5 其中一个检查方法是通过 !==：

```
> const x = NaN;
> x !== x
true
```

更具描述性的方法是通过全局函数 isNaN()：

```
> const x = NaN;
> isNaN(x)
true
```

但是，该方法强制将非数字转化为数字，如果结果是 NaN（通常不是你想要的）就会返回true：

```
> isNaN('???')
true
```

新的 Number.isNaN() 不存在这个问题，因为它不会强制把参数转化为 numbers：

```
> Number.isNaN('???')
false
```

\#### 5.3.1.3 Number.parseFloat 和 Number.parseInt

&#x20;下面2个方法跟同名的全局方法效果相同。为了完整起见，把它们加到 Number 中；现在所有number相关的方法都在这可用：

* `Number.parseFloat(string)`[1](https://exploringjs.com/es6/leanpub-endnotes.html#fn-numbers\_1)
* `Number.parseInt(string, radix)`[2](https://exploringjs.com/es6/leanpub-endnotes.html#fn-numbers\_2)

#### 5.3.2 Number.EPSILON

尤其是小数部分，舍入的错误会成为 JavaScript 中的一个问题。比如，0.1 和 0.2 无法精确表示，如果把它们加起来去和 0.3 （同样无法精确表示）比较，就会注意到。

```
> 0.1 + 0.2 === 0.3
false
```

在比较浮点数时，Number.EPSILON 指定了一个比较合理的误差范围。它提供了一种比较浮点值更好的方法，如下所示。

```javascript
function epsEqu(x, y) {
    return Math.abs(x - y) < Number.EPSILON;
}
console.log(epsEqu(0.1+0.2, 0.3)); // true
```

#### 5.3.3 Number.isInteger(number)

JavaScript 只有浮点数（双精度）。一次，整数只有没有小数部分的浮点数。

如果一个number是一个数字并且没有小数，Number.isInteger(number) 就会返回 true。

```
> Number.isInteger(-17)
true
> Number.isInteger(33)
true
> Number.isInteger(33.1)
false
> Number.isInteger('33')
false
> Number.isInteger(NaN)
false
> Number.isInteger(Infinity)
false
```

#### 5.3.4 安全整数

JavaScript 数字的存储空间只够表示 53 位有符号的整数。也就是说，整数 i 在 -2^53 < i < 2^53 是安全的。稍后解释这意味着什么。以下属性有助于确定一个 JavaScript 整数是否安全：

* `Number.isSafeInteger(number)`
* `Number.MIN_SAFE_INTEGER`
* `Number.MAX_SAFE_INTEGER`

安全整数的概念以 JavaScript 中数学整数的表示方式为中心。在（-2^53, 2^53）范围内（不包括下限和上限），JavaScript 整数是安全的：它们与它们代表的数学整数之间存在一对一的映射关系。

超出这个范围，JavaScript 整数是不安全的：2个或多个数学整数在 JavaScript 表示的相同整数。例如，从 2^53 开始，JavaScript 只能表示每隔1个数学整数：

```
> Math.pow(2, 53)
9007199254740992

> 9007199254740992
9007199254740992
> 9007199254740993
9007199254740992
> 9007199254740994
9007199254740994
> 9007199254740995
9007199254740996
> 9007199254740996
9007199254740996
> 9007199254740997
9007199254740996
```

因此，一个安全的 JavaScript 整数是明确表示单个数学整数的整数。

\#### 5.3.4.1 与安全整数相关的静态 Number 属性

指定安全整数的下限和上限的2个静态 Number 属性可以这么定义：

```
Number.MAX_SAFE_INTEGER = Math.pow(2, 53)-1;
Number.MIN_SAFE_INTEGER = -Number.MAX_SAFE_INTEGER;
```

Number.isSafeInteger() 判断一个 JavaScript 数字是否是一个安全整数，可以如下定义：

```javascript
Number.isSafeInteger = function (n) {
    return (typeof n === 'number' &&
        Math.round(n) === n &&
        Number.MIN_SAFE_INTEGER <= n &&
        n <= Number.MAX_SAFE_INTEGER);
}
```

对于一个给定的 n，改函数先检查 n 是否为一个 number 和一个整数。如果两者均校验成功，那么大于等于 MIN\_SAFE\_INTEGER 和 小于等于 MAX\_SAFE\_INTEGER，n 是安全的。

\#### 5.3.4.2 整数的计算何时是正确的？

我们如何确保整数计算的结果是正确的？比如，下面的结果显然不正确：

```
> 9007199254740990 + 3
9007199254740992
```

我们有 2 个安全的操作数，但有一个不安全的结果：

```
> Number.isSafeInteger(9007199254740990)
true
> Number.isSafeInteger(3)
true
> Number.isSafeInteger(9007199254740992)
false
```

下面的结果也不正确：

```
> 9007199254740995 - 10
9007199254740986
```

这次结果是安全的，但其中一个操作数不是：

```
> Number.isSafeInteger(9007199254740995)
false
> Number.isSafeInteger(10)
true
> Number.isSafeInteger(9007199254740986)
true
```

因此，只有所有的操作数和结果都是安全的，才能保证应用一个整数运算符 op 的结果是正确的。更为正式的来说：

```
isSafeInteger(a) && isSafeInteger(b) && isSafeInteger(a op b)
```

暗示 a op b 是一个正确的结果。

{% hint style="info" %}
本节出处

“[Clarify integer and safe integer resolution](https://mail.mozilla.org/pipermail/es-discuss/2013-August/032991.html)”, email by Mark S. Miller to the es-discuss mailing list.
{% endhint %}

### 5.4 新的 Math 功能

全局对象 Math 在 ECMAScript6 中有几个新方法。

#### 5.4.1 多种数值功能

\#### 5.4.1.1 Math.sign(x)

Math.sign(x) 返回：

* \-1 如果 x 是一个负数（包括 -Infinity）
* 0 如果 x 是 0
* \+1 如果 x 是一个正数（包括 Infinity）
* NaN 如果 x 是 NaN 或者不是一个 number

例子：

```
> Math.sign(-8)
-1
> Math.sign(3)
1

> Math.sign(0)
0
> Math.sign(NaN)
NaN

> Math.sign(-Infinity)
-1
> Math.sign(Infinity)
1
```

\#### 5.4.1.2 Math.trunc(x)

Math.trunc 用来移除 x 的小数部分。是其他 rounding 方法 Math.floor() Math.ceil() 和 Math.round() 的补充。

```
> Math.trunc(3.1)
3
> Math.trunc(3.9)
3
> Math.trunc(-3.1)
-3
> Math.trunc(-3.9)
-3
```

你也可以这么实现 Math.trunc()：

```javascript
function trunc(x) {
    return Math.sign(x) * Math.floor(Math.abs(x));
}
```

\#### 5.4.1.2 Math.cbrt(x)

Math.cbrt(x) 返回 x （∛x）的立方根。

```
> Math.cbrt(8)
2
```

#### 5.4.2 用 0 而不是 1 求幂和对数

如果一个小片段在0之后，则它可以表示更精确。我将用小数来演示一下（JavaScript 的 number 在内部用 2 为基数存储， but the same reasoning applies）。





#### 5.4.3 以 2 和 10 为底的对数

#### 5.4.4 支持编译为 JavaScript

#### 5.4.5 按位运算

#### 5.4.6 三角函数方法

### 5.5 FAQ: numbers

#### 5.5.1 如何使用超出JavaScript 53 位范围的整数？

\
