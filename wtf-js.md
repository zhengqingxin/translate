# 什么鬼 JavaScript ？

> 本文转载自：[众成翻译](http://www.zcfy.cc)
> 译者：[郑 farmer](http://www.zcfy.cc/@undefined)
> 审校: [huangxiaolu](http://www.zcfy.cc/@huangxiaolu)
> 链接：[http://www.zcfy.cc/article/3844](http://www.zcfy.cc/article/3844)
> 原文：[https://github.com/denysdovhan/wtfjs](https://github.com/denysdovhan/wtfjs)

# WTF JavaScript ？

> 一个关于 JavaScript 的花式玩法列表。

JavaScript 是一个伟大的语言。它有简单的语法，完善的生态系统，最重要的是，有一个庞大的社区。

同时，我们都知道，JavaScript 有很多有趣的“潜规则”。其中有一些经常在日常工作中给我们添麻烦，而有些可以给我们带来帮助，让我们大笑起来。

这篇文章的思想源于[Brian Leroux](https://twitter.com/brianleroux)，受到他在[2012年dotJS上的演讲“WTFJS”](https://www.youtube.com/watch?v=et8xNAc2ic8)的高度启发。

[![dotJS 2012 - Brian Leroux - WTFJS](http://p0.qhimg.com/t013eb93a0305179a66.jpg)](https://www.youtube.com/watch?v=et8xNAc2ic8)

# 💪🏻动机

> 只是为了好玩。
> 
> —[“Just for Fun: The Story of an Accidental Revolutionary”](https://en.m.wikipedia.org/wiki/Just_for_Fun), Linus Torvalds

这篇文章主要收集了一些有趣(qí pā)的例子，并尽可能地解释它们如何工作的。因为这样可以让我们学习到很多之前不知道的东西。

如果你是个初学者，可以使用此文章来更深入了解JavaScript。我希望这篇文章会激励你花更多的时间阅读规范。

如果你是高级开发人员，你可以将这些示当做你公司面试的重要资源。同时，这些例子在准备面试时会很方便。

无论如何，阅读这篇文章，保证你会收获新的东西。

# ✍🏻文中符号说明

`// ->`用于显示表达式的结果。例如：

```
1 + 1 // -> 2

```

`// >`表示 console.log 或着其他什么输出。例如：

```
console.log('hello, world!') // > hello, world!

```

`//`是一个注释语句。例：

```
// Assigning a function to foo constant
const foo = function () {}

```

# 👀示例

## `[]` 等于`！[]`

数组等于非数组？！

```
[] == ![] // -> true

```

### 💡说明：

*   [**12.5.9** Logical NOT Operator (`!`)](https://www.ecma-international.org/ecma-262/#sec-logical-not-operator)

*   [**7.2.13** Abstract Equality Comparison](https://www.ecma-international.org/ecma-262/#sec-abstract-equality-comparison)

## [](#true-is-false)true 是 false

```
!!'false' ==  !!'true'  // -> true
!!'false' === !!'true' // -> true

```

### 💡说明：

按照下面这几步思考：

```
true == 'true'    // -> true
false == 'false'  // -> false

// 'false' 不是空字符串，因此它的值为真值
!!'false' // -> true
!!'true'  // -> true

```

*   [**7.2.13** Abstract Equality Comparison](https://www.ecma-international.org/ecma-262/#sec-abstract-equality-comparison)

## fooNaN

学院派 JavaScript 中的一个笑话：

```
"foo" + + "bar" // -> 'fooNaN'

```

### 💡说明：

该表达式可以转换为 `'foo'+（+'bar'）`，`+‘bar’`将 “bar” 转换为`NaN`。

*   [**12.8.3** The Addition Operator (`+`)](https://www.ecma-international.org/ecma-262/#sec-addition-operator-plus)

## `NaN` 不等于 `NaN`

```
NaN === NaN // -> false

```

### 💡说明：

规范中定义了这种行为背后的逻辑：

> 1.  如果 `Type（x）`不等于`Type（y）`，则返回false。
>     
>     
>     
> 
> 2.  如果 Type（x）是Number，那么
>     
>     
>     
> 
> ```
> 1\.  If `x` is **NaN**, return **false**.
> 
> 2\.  If `y` is **NaN**, return **false**.
> 
> 3\.  … … …
> 
> ```
> 
> -- [7.2.14Strict Equality Comparison](https://www.ecma-international.org/ecma-262/#sec-strict-equality-comparison)

遵循IEEE的NaN定义：

> 四个相互排斥的关系是可能的：小于，等于，大于和无序。当至少一个操作是 NaN 时，最后一种情况出现。每个 NaN 相对于所有东西来说都是无序的，包括自己。
> 
> [“IEEE754 中 NaN值的所有比较都返回false的理由是什么？”](https://stackoverflow.com/questions/1565164/1573715#1573715) --StackOverflow

## It's a fail

可能你不会相信，但...

```
(![]+[])[+[]]+(![]+[])[+!+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]
// -> 'fail'

```

### 💡说明：

将这条语句做几次分割，我们来分析一下结果：

```
(![]+[]) // -> 'false'
![]      // -> false

```

我们尝试将`[]`置为`false`。但是通过一些内部函数调用，最终转换为一个字符串.

```
(![]+[].toString()) // 'false'

```

想想一个字符串作为数组，我们可以通过`[0]`访问它的第一个字符：

```
'false'[0] // -> 'f'

```

剩下的部分显而易见的，但是 `i` 很特别。在 `'falseundefined'`里取到了索引为10.

## `[]`为真，但不是`true`

数组为真值，但是它不等于`true`。

```
!![]       // -> true
[] == true // -> false

```

### 💡说明：

以下是ECMA-262规范中相应部分的链接：

*   [**12.5.9** Logical NOT Operator (`!`)](https://www.ecma-international.org/ecma-262/#sec-logical-not-operator)

*   [**7.2.13** Abstract Equality Comparison](https://www.ecma-international.org/ecma-262/#sec-abstract-equality-comparison)

    ## null是假的（falsy），但不等于`false`

尽管`null`是假值，但它不等于`false`。

```
!!null        // -> false
null == false // -> false

```

但是，其他的假值，如`0`或`''`等于`false`。

```
0 == false  // -> true
'' == false // -> true

```

### 💡说明：

与前面一样，这是一个相应的链接：

*   [**7.2.13** Abstract Equality Comparison](https://www.ecma-international.org/ecma-262/#sec-abstract-equality-comparison)

    ## 最小的值大于零

`Number.MIN_VALUE`是最小的数字，但还是大于零：

```
Number.MIN_VALUE > 0 // -> true

```

### 💡说明：

> `Number.MIN_VALUE`是`5e-324`，即可以在浮点精度内表示的最小正数，即尽可能接近于零。它定义了浮点数给能给你的最小值。
> 
> 现在，最小的正数是`Number.NEGATIVE_INFINITY`，尽管这在严格意义上并不是真正的数字。
> 
> [“为什么在JavaScript中为0小于Number.MIN_VALUE”？](https://stackoverflow.com/questions/26614728/why-is-0-less-than-number-min-value-in-javascript)--StackOverflow
> 
> *   [**20.1.2.9** Number.MIN_VALUE](https://www.ecma-international.org/ecma-262/#sec-well-known-symbols)

## 数组相加

如果两个数组相加会怎样？

```
[1, 2, 3] + [4, 5, 6]  // -> '1,2,34,5,6'

```

### 💡说明：

分开来看，它看起来像这样

```
[1, 2, 3] + [4, 5, 6]
// joining
[1, 2, 3].join() + [4, 5, 6].join()
// concatenation
'1,2,3' + '4,5,6'
// ->
'1,2,34,5,6'

```

## `undefined` 和 `Number`

如果我们没有将任何参数传递给 Number 的构造函数，我们将得到`0`。`undefined`是一个分配给形式参数的值，它没有实际的参数，因此您可能希望Number（无参数）不定义为其参数的值。然而当我们传一个`undefined`的时候，我们将得到`NaN`。

```
Number()          // -> 0
Number(undefined) // -> NaN

```

### 💡说明：

根据标准：

1.  如果没有参数传递给这个函数，`n`为`+0`。

2.  否则，让 n 等于否则，n 为`ToNumber(value)`

3.  所以在这里传入`undefined`，则`ToNumber（undefined）`应返回`NaN`。

这里有相应的资料：

*   [**20.1.1** The Number Constructor](https://www.ecma-international.org/ecma-262/#sec-number-constructor)

*   [**7.1.3** ToNumber(`argument`)](https://www.ecma-international.org/ecma-262/#sec-tonumber)

## `parseInt`是个坏小子

parseInt 有一些怪癖比如：

```
parseInt('f*ck');     // -> NaN
parseInt('f*ck', 16); // -> 15

```

💡说明：发生这种情况是因为 `parseInt` 将逐字符的解析，直到它遇到解析不了的字符。`'f * ck'` 中的 `f` 是十六进制15。

将`Infinity`解析到整数是...

```
//
parseInt('Infinity', 10) // -> NaN
// ...
parseInt('Infinity', 18) // -> NaN...
parseInt('Infinity', 19) // -> 18
// ...
parseInt('Infinity', 23) // -> 18...
parseInt('Infinity', 24) // -> 151176378
// ...
parseInt('Infinity', 29) // -> 385849803
parseInt('Infinity', 30) // -> 13693557269
// ...
parseInt('Infinity', 34) // -> 28872273981
parseInt('Infinity', 35) // -> 1201203301724
parseInt('Infinity', 36) // -> 1461559270678...
parseInt('Infinity', 37) // -> NaN

```

小心null：

```
parseInt(null, 24) // -> 23

```

💡说明：

> 它将 `null` 转换为字符串`“null”`，并尝试转换它。对于 0 到 23 进制，没有可以转换的数字，因此返回`NaN`。在 24 进制时，将第14个字母的“n”可以转换位数字。在31进制时，第二十一个字母“u”，解码整个字符串。在37时，不再有可以生成的有效数字集合，所以返回NaN。
> 
> <span style="color: rgb(102, 102, 102);">见StackOverflow：</span><span style="color: rgb(102, 102, 102);">[“parseInt(null,24) === 23…等等，什么鬼？“（</span>[https://stackoverflow.com/questions/6459758/parseintnull-24-23-wait-what](https://stackoverflow.com/questions/6459758/parseintnull-24-23-wait-what)<span style="color: rgb(102, 102, 102);">）</span>

不要忘记八进制：

```
parseInt('06'); // 6parseInt('08'); // 8 如果支持ECMAScript 5parseInt('08'); // 0 如果不支持ECMAScript 5

```

💡说明：这是因为 parseInt 第二个参数代表进制。如果没有提供，并且字符串以0开始，它将被解析为八进制数。

## `true`和`false`做计算操作

我们做一些计算操作：

```
true + true // -> 2
(true + true) * (true + true) - true // -> 3

```

嗯...🤔

### 💡说明：

我们可以通过 Number 构造函数将这些值强制转换为数字。很明显，`true`将被转换成`1`：

```
Number(true) // -> 1

```

`+`运算符尝试将其值转换成数字。它可以转换整数或者浮点数形式的字符串，以及非字符串值`true`，`false`和`null`。如果不能解析，会转为`NaN`。这意味着我们可以强制`true`转为`1`：

```
+true // -> 1

```

当你执行加法或乘法时，`ToNumber`方法被调用。根据规范，该方法返回：

> 如果`argument`为`true`，则返回`1`。如果`argument`为false，则返回`+0`。

这就是为什么我们可以与布尔值相加，视为常规数字并获得正确的结果。

相应文档：

*   [**12.5.6** Unary `+` Operator](https://www.ecma-international.org/ecma-262/#sec-unary-plus-operator)

*   [**12.8.3** The Addition Operator (`+`)](https://www.ecma-international.org/ecma-262/#sec-addition-operator-plus)

*   [**7.1.3** ToNumber(`argument`)](https://www.ecma-international.org/ecma-262/#sec-tonumber)

## HTML 注释在 JavaScript 中有效

你可能不信，`<!--`(在HTML中的注释)在 JavaScript 中是有效的

震惊了？HTML 类似的注释，旨在让没法解析`<script>`标签浏览器优雅降级。例如现在不再流行的 Netscape 1.x 的这类浏览器。所以实际上，将 HTML 注释放在你的脚本标签中也没有任何意义了。

然而由于 Node.js 基于 V8 引擎，Node.js运行时也支持类似 HTML 的注释。而且，它们是规范的一部分：

*   [**B.1.3** HTML-like Comments](https://www.ecma-international.org/ecma-262/#sec-html-like-comments)

    ## `NaN` 是数字

`NaN`的类型是 `number`：

```
typeof NaN            // -> 'number'

```

### 💡说明：

`typeof`和`instanceof`运算符的工作原理：

*   [**12.5.5** The `typeof` Operator](https://www.ecma-international.org/ecma-262/#sec-typeof-operator)

*   [**12.10.4** Runtime Semantics: InstanceofOperator(`O`,`C`)](https://www.ecma-international.org/ecma-262/#sec-instanceofoperator)

    ## `[]`和`null`是对象

    typeof []   // -> 'object'
    typeof null // -> 'object'

    // however
    null instanceof Object // false

### 💡说明：

`typeof`在规范中的定义：

*   [**12.5.5** The `typeof` Operator](https://www.ecma-international.org/ecma-262/#sec-typeof-operator)
    根据规范，`typeof`运算符返回一个字符串（[typeof Operator Results](https://www.ecma-international.org/ecma-262/#table-35).）对于`null`，一般的对象，标准的外来对象，以及非标准的外来对象，没有实现`[[Call]]`，将会返回字符串“object”。

然而其实，你可以使用 toString 方法检查对象的类型。

```
Object.prototype.toString.call([])
// -> '[object Array]'

Object.prototype.toString.call(new Date)
// -> '[object Date]'

Object.prototype.toString.call(null)
// -> '[object Null]'

```

## 迷之数字

```
999999999999999  // -> 999999999999999
9999999999999999 // -> 10000000000000000

10000000000000000       // -> 10000000000000000
10000000000000000 + 1   // -> 10000000000000000
10000000000000000 + 1.1 // -> 10000000000000002

```

### 💡说明：

这是由 IEEE 754-2008 二进制浮点运算标准引起的。在这个标准之上，它会舍入到最接近的偶数。阅读更多：

*   [**6.1.6** The Number Type](https://www.ecma-international.org/ecma-262/#sec-ecmascript-language-types-number-type)

*   维基百科上的[IEEE 754](https://en.m.wikipedia.org/wiki/IEEE_754)

## `0.1 + 0.2` 的精度问题

众所周知的笑话。`0.1 + 0.2`有个非常牛X的精确度：

```
0.1 + 0.2 // -> 0.30000000000000004
(0.1 + 0.2) === 0.3 // -> false

```

### 💡说明：

“[浮点数字迷题破解？](https://stackoverflow.com/questions/588004/is-floating-point-math-broken)”--StackOverflow

> 其实程序中的常数`0.2`和`0.3`也是接近它们真正的值。离`0.2`最近的`double`数要比有理数`0.2`大，但是离`0.3`最近的`double`数要比有理数`0.3`小。`0.1`和`0.2`的总和大于有理数`0.3`，因此不同于的代码中的常数。

这个问题是众所周知的，这里有一个名为[0.30000000000000004.com](http://0.30000000000000004.com/)的网站。它发生在使用浮点数的每种语言中，而不仅仅是JavaScript。

## 数字补丁

你可以添加自己的方法来包装对象，如`Number`或`String`。

```
Number.prototype.isOne = function () {
  return Number(this) === 1
}

1.0.isOne() // -> true
1..isOne()  // -> true
2.0.isOne() // -> false
(7).isOne() // -> false

```

### 💡说明：

显然，你可以像 JavaScript 中的任何其他对象一样扩展 `Number` 对象。但是，如果定义的方法的方式不符合规范，则不建议使用。以下是`Number`的属性列表：

*   [**20.1** Number Objects](https://www.ecma-international.org/ecma-262/#sec-number-objects)

    ## 三个数字比较

    1 < 2 < 3 // -> true
    3 > 2 > 1 // -> false

### 💡说明：

为什么这样呢？问题在于表达式的第一部分。以下是它的工作原理：

```
1 < 2 < 3 // 1 < 2 -> true
true  < 3 // true -> 1
1     < 3 // -> true

3 > 2 > 1 // 3 > 2 -> true
true  > 1 // true -> 1
1     > 1 // -> false

```

我们可以用`> =`来修复此问题：

```
3 > 2 >= 1 // true

```

详细了解规范中的关系运算符：

*   [**12.10** Relational Operators](https://www.ecma-international.org/ecma-262/#sec-relational-operators)

    ## 有趣的数学运算

通常 JavaScript 中的算术运算的结果可能是非常难以预料的。考虑这些例子：

```
 3  - 1  // -> 2
 3  + 1  // -> 4
'3' - 1  // -> 2
'3' + 1  // -> '31'

'' + '' // -> ''
[] + [] // -> ''
{} + [] // -> 0
[] + {} // -> '[object Object]'
{} + {} // -> '[object Object][object Object]'

'222' - -'111' // -> 333

[4] * [4]       // -> 16
[] * []         // -> 0
[4, 4] * [4, 4] // NaN

```

### 💡说明：

前四个例子发生了什么？下面这个列表总结了 JavaScript 中的相加运算：

```
Number  + Number  -> addition
Boolean + Number  -> addition
Boolean + Boolean -> addition
Number  + String  -> concatenation
String  + Boolean -> concatenation
String  + String  -> concatenation

```

剩下的例子呢？`[]`和`{}`在做相加运算之前，偷偷调用了`ToPrimitive`和`ToString`方法，了解详细规范参考：

*   [**12.8.3** The Addition Operator (`+`)](https://www.ecma-international.org/ecma-262/#sec-addition-operator-plus)

*   [**7.1.1** ToPrimitive(`input` [,`PreferredType`])](https://www.ecma-international.org/ecma-262/#sec-toprimitive)

*   [**7.1.12** ToString(`argument`)](https://www.ecma-international.org/ecma-262/#sec-tostring)

    ## 正则的相加运算

你知道你可以这样做相加运算吗？

```
// 实现toString方法
RegExp.prototype.toString = function() {
  return this.source
}

/7/ - /5/ // -> 2

```

### 💡说明：

*   [**21.2.5.10** get RegExp.prototype.source](https://www.ecma-international.org/ecma-262/#sec-get-regexp.prototype.source)

    ## 字符串不是`String`的实例

    'str' // -> 'str'
    typeof 'str' // -> 'string'
    'str' instanceof String // -> false

### 💡说明：

`String` 的`construnctor`返回一个字符串 ：

```
typeof String('str')   // -> 'string'
String('str')          // -> 'str'
String('str') == 'str' // -> true

```

我们来试一下`new`：

```
new String('str') == 'str' // -> true
typeof new String('str')   // -> 'object'

```

`object`?那是啥？

```
new String('str') // -> [String: 'str']

```

有关`String`构造函数的更多信息：

*   [**21.1.1** The String Constructor](https://www.ecma-international.org/ecma-262/#sec-string-constructor)

    ## 用反引号调用函数

我们来声明一个将所有参数返回到控制台中的函数：

```
function f(...args) {
  return args
}

```

毫无疑问，你可以这样调用这个函数：

```
f(1, 2, 3) // -> [ 1, 2, 3 ]

```

但你知道反引号可以调用任何函数吗？

```
f`true is ${true}, false is ${false}, array is ${[1,2,3]}`
// -> [ [ 'true is ', ', false is ', ', array is ', '' ],
// ->   true,
// ->   false,
// ->   [ 1, 2, 3 ] ]

```

### 💡说明：

如果你熟悉Tagged template literals（标签模板字面量）那么可能你感觉这很正常，在上面的例子中，`f`函数是模板的标签。模板文字之前的标签允许您使用函数解析模板文字。标签函数的第一个参数是一个包含字符串的数组。其余的参数与表达式有关。比如：

```
function template(strings, ...keys) {
  // do something with strings and keys…
}

```

这是一个[有魔力](http://mxstbr.blog/2016/11/styled-components-magic-explained/)的类库，名为[💅 styled-components](https://www.styled-components.com/)，这在 React 社区很受欢迎。

规范：

*   [**12.3.7** Tagged Templates](https://www.ecma-international.org/ecma-262/#sec-tagged-templates)

    ## Call call call

> 由[@cramforce](http://twitter.com/cramforce)发现

```
console.log.call.call.call.call.call.apply(a => a, [1, 2])

```

### 💡说明：

前方高能！看后可能会损伤大量脑细胞。尝试在你脑海中重现此代码：我们正在使用`apply`方法调用`call`方法。阅读更多：

*   [**19.2.3.3** Function.prototype.call(`thisArg`, ...`args`)](https://www.ecma-international.org/ecma-262/#sec-function.prototype.call)

*   [**19.2.3.1 ** Function.prototype.apply(`thisArg`, `argArray`)](https://www.ecma-international.org/ecma-262/#sec-function.prototype.apply)

    ## `constructor`属性

    const c = 'constructor'
    c[c][c]('console.log("WTF?")')() // > WTF?

### 💡说明：

让我们逐步思考这个例子：

```
// Declare a new constant which is a string 'constructor'
const c = 'constructor'

// c is a string
c // -> 'constructor'

// Getting a constructor of string
c[c] // -> [Function: String]

// Getting a constructor of constructor
c[c][c] // -> [Function: Function]

// Call the Function constructor and pass
// the body of new function as an argument
c[c][c]('console.log("WTF?")') // -> [Function: anonymous]

// And then call this anonymous function
// The result is console-logging a string 'WTF'
c[c][c]('console.log("WTF?")')() // > WTF

```

`Object.prototype.constructor`返回一个`Object`用来创建实例函数的引用，在字符串中，它是`String`，数字则为`Number`等等。

*   [Object.prototype.constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor)

*   [**19.1.3.1** Object.prototype.constructor](https://www.ecma-international.org/ecma-262/#sec-object.prototype.constructor)

## 用对象作为对象属性的key

```
{ [{}]: {} } // -> { '[object Object]': {} }

```

### 💡说明：

为什么这样？这里应用到了_Computed property name_。当在方括号中传递一个对象时，它会将对象强制转换为字符串，所以我们得到一个属性键`'[object Object]'`和值 `{}`。

同样的方式，我们还可以像这样使用中括号：

```
({[{}]:{[{}]:{}}})[{}][{}] // -> {}

// structure:
// {
//   '[object Object]': {
//     '[object Object]': {}
//   }
// }

```

阅读更多参考：

*   [Object initializer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)

*   [**12.2.6** Object Initializer](http://www.ecma-international.org/ecma-262/6.0/#sec-object-initializer)

## 使用`__proto__`访问原型

大家都知道，原始数据类型是没有原型的。但是，如果我们尝试对它们获取**proto**，我们会得到这样的：

```
(1).__proto__.__proto__.__proto__ // -> null

```

### 💡说明：

这是因为原始数据类型没有原型，它将使用`ToObject`方法包装在包装器对象中。分步来看:

```
(1).__proto__ // -> [Number: 0]
(1).__proto__.__proto__ // -> {}
(1).__proto__.__proto__.__proto__ // -> null

```

以下是有关`__proto__`的更多信息：

## `${{Object}}`

下面的表达结果是什么？

```
`${{Object}}`

```

答案是：

```
// -> '[object Object]'

```

### 💡说明：

我们使用_Shorthand property notation_表示法定义了一个带有属性`Object` 的对象：

```
{ Object: Object }

```

然后我们将这个对象传递给模板，所以`toString`方法被调用。这就是为什么我们得到字符串'`[object Object]'`。

*   [**12.2.9** Template Literals](https://www.ecma-international.org/ecma-262/#sec-template-literals)

*   [Object initializer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)

## 使用默认值进行解构

思考这个例子：

```
let x, { x: y = 1 } = { x }; y;

```

上面的例子可能是一个很好的面试题。`y`的值是多少？答案是：

```
// -> 1

```

### 💡说明：

```
let x, { x: y = 1 } = { x }; y;
//  ↑       ↑           ↑    ↑
//  1       3           2    4

```

以上示例中：

1.  我们定义了一个没有值的`x`，它的值是`undefined`

2.  然后我们将`x`的值打包到对象属性`x`中。

3.  然后我们使用解构来提取`x`的值，并希望赋值给`y`。如果未定义该值，那么将用`1`作为默认值。

4.  返回`y`的值。

5.  [Object initializer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)

## Dots and spreading

下面是个关于数组解构的有趣例子思考这个：

```
[...[...'...']].length // -> 3

```

### 💡说明：

为什么是3？当我们使用扩展运算符时，`@@ iterator`方法被调用，返回迭代器用于获取要迭代的值。字符串默认是按字母迭代。解构后，我们将这些字符打包成一个数组。然后再次解构这个数组，然后再打包成数组。

一个`'...'`字符串由三个`.`组成，因此结果数组的长度将为3。

逐步思考：

```
[...'...']             // -> [ '.', '.', '.' ]
[...[...'...']]        // -> [ '.', '.', '.' ]
[...[...'...']].length // -> 3

```

显然，我们可以像我们想要的那样解构和包装数组的元素：

```
[...'...']                 // -> [ '.', '.', '.' ]
[...[...'...']]            // -> [ '.', '.', '.' ]
[...[...[...'...']]]       // -> [ '.', '.', '.' ]
[...[...[...[...'...']]]]  // -> [ '.', '.', '.' ]
// and so on …

```

## 标签

可能很多人不知道 JavaScript 中的标签。他们很有趣：

```
foo: {
  console.log('first');
  break foo;
  console.log('second');
}

// > first
// -> undefined

```

### 💡说明：

带标签的语句与`break`或`continue`语句一起使用。你可以使用标签来标识循环，然后使用`break`或`continue`语句来控制程序中断或者继续执行。

在上面的例子中，我们定义了一个标签`foo`。然后执行了 `console.log（'first'）;`，然后中断执行。

详细了解 JavaScript 中的标签：

*   [**13.13** Labelled Statements](https://tc39.github.io/ecma262/#sec-labelled-statements)

*   [Labeled statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)

## 嵌套标签

```
a: b: c: d: e: f: g: 1, 2, 3, 4, 5; // -> 5

```

### 💡说明：

和之前一样，参考下面的链接：

*   [**12.16** Comma Operator (`,`)](https://www.ecma-international.org/ecma-262/#sec-comma-operator)

*   [**13.13** Labelled Statements](https://tc39.github.io/ecma262/#sec-labelled-statements)

*   [Labeled statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)

## `try..catch`的坑

这个表达将返回什么？`2`还是`3`？

```
(() => {
  try {
    return 2;
  } finally {
    return 3;
  }
})()

```

答案是3。惊讶吗？

### 💡说明：

## 这是多重继承吗？

看下面的例子：

```
new (class F extends (String, Array) { }) // -> F []

```

这是多重继承吗？不是。

### 💡说明：

有趣的部分是`extends`后面的语句`（String，Array）`。分组运算符总是返回其最后一个参数，所以`（String，Array）`实际上是只返回了`Array`。这意味着我们刚刚创建了一个Array 的继承类。

## yields 它自己的 generator

考虑这个例子，

```
(function* f() { yield f })().next()
// -> { value: [GeneratorFunction: f], done: false }

```

如你所见，返回的值是一个值等于`f`的对象。在这种情况下，我们可以这样做：

```
(function* f() { yield f })().next().value().next()
// -> { value: [GeneratorFunction: f], done: false }

// and again
(function* f() { yield f })().next().value().next().value().next()
// -> { value: [GeneratorFunction: f], done: false }

// and again
(function* f() { yield f })().next().value().next().value().next().value().next()
// -> { value: [GeneratorFunction: f], done: false }

// and so on
// …

```

### 💡说明：

要了解为什么这样，请阅读这些部分：

## class 的 class

考虑这个模糊的语法：

```
(typeof (new (class { class () {} }))) // -> 'object'

```

看来我们一个类中声明一个类。按理来说应该会报错，但是，我们得到一个`“object”`字符串。

### 💡说明：

由于 ECMAScript 5 的时代，允许用关键字作为属性名称。所以想一想这个简单的对象例子：

```
const foo = {
  class: function() {}
};

```

用 ES6 则简化成如下方法定义。此外，类还可能是匿名的。所以如果我们删除 function，我们将得到：

```
class {
  class() {}
}

```

默认情况，类的返回总是一个简单的对象。它的 `typeof` 应该返回 `'object'`。

在这里了解更多：

## 非强转对象

有一个很常用的方法，用来避免强制类型转换。比如：

```
function nonCoercible(val) {
  if (val == null) {
    throw TypeError('nonCoercible should not be called with null or undefined')
  }

  const res = Object(val)

  res[Symbol.toPrimitive] = () => {
    throw TypeError('Trying to coerce non-coercible object')
  }

  return res
}

```

现在我们可以这样使用：

```
// objects
const foo = nonCoercible({foo: 'foo'})

foo * 10      // -> TypeError: Trying to coerce non-coercible object
foo + 'evil'  // -> TypeError: Trying to coerce non-coercible object

// strings
const bar = nonCoercible('bar')

bar + '1'                 // -> TypeError: Trying to coerce non-coercible object
bar.toString() + 1        // -> bar1
bar === 'bar'             // -> false
bar.toString() === 'bar'  // -> true
bar == 'bar'              // -> TypeError: Trying to coerce non-coercible object

// numbers
const baz = nonCoercible(1)

baz == 1             // -> TypeError: Trying to coerce non-coercible object
baz === 1            // -> false
baz.valueOf() === 1  // -> true

```

### 💡说明：