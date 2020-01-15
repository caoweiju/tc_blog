---
title: JSON的正确认识
date: 2019-01-17 00:02:29
tags:
- json
categories: 
- json
---
# JSON
>`JSON(JavaScript Object Notation)` 是一种轻量级的数据交换格式。易于人阅读和编写。同时也易于机器解析和生成。它基于`JavaScript Programming Language, Standard ECMA-262 3rd Edition - December 1999`的一个子集。

* `JSON` 指的是 JavaScript 对象表示法（`JavaScript Object Notation`）
* `JSON` 是轻量级的文本数据交换格式
* `JSON` 独立于语言：JSON 使用` Javascript`语法来描述数据对象，但是 JSON 仍然独立于语言和平台。JSON 解析器和 JSON 库支持许多不同的编程语言。 目前非常多的动态（`PHP，JSP，.NET`）编程语言都支持JSON。
* `JSON` 具有自我描述性，更易理解

<!-- more -->

## JSON数据格式
`JSON`大致3种结构，`JSON`对象、`JSON`数组和`JSON`对象和数组嵌套。
### Object对象
`Object`是一个无序`‘名称/值’对`集合，一个对象以`{`（左括号）开始，`}`（右括号）结束。每个`名称`后跟一个`:`（冒号）；`‘名称/值’ 对`之间使用`,`（逗号）分隔。

![clipboard.png](http://json.org/object.gif)
**其中键值对中的键必须是`string`类型，值可以是json三种`Object|Array|value`类型中的一种，`string`属于一种`value`，`value`包含`string、null、number`等，后面第三种会讲**
实例：
````
{}
{"aa": true}
{"bb": "3333"}
{"cc": null}
{"dd": {"ee": ["ff","gg"]}}
````
### Array数组
数组是值（value）的有序集合。一个数组以`[`（左中括号）开始，`]`（右中括号）结束。值之间使用`,`（逗号）分隔。

![clipboard.png](https://json.org/array.gif)

示例：
````
[]
["aa","bb"]
[[1,2],[3,4]]
[{"aa": []},{bb: []}]
````
### 值Value
值`（value）`可以是`双引号括起来的字符串（string）、数值(number)、true、false、 null、对象（object）或者数组（array）`。这些结构可以嵌套。

![clipboard.png](https://json.org/value.gif)

#### string
`string`是`value`中比较特殊的需要注意的一种；字符串（string）是由双引号包围的任意数量Unicode字符的集合，使用反斜线转义。一个字符（character）即一个单独的字符串（character string）。

![clipboard.png](https://json.org/string.gif)

示例：
````
"aaa"
"\ttab"
"\nnew line"
````
看起来很简单啊；结果我们去V8环境中运行了一下，
````
JSON.parse("\nnewline");
VM5802:2 Uncaught SyntaxError: Unexpected token e in JSON at position 2
    at JSON.parse (<anonymous>)
    at <anonymous>:1:6
(anonymous) @ VM5801:1
JSON.parse("\ttab");
VM5836:1 Uncaught SyntaxError: Unexpected token a in JSON at position 2
    at JSON.parse (<anonymous>)
    at <anonymous>:1:6
````
蓝瘦了，不是说好的格式吗？反向测试：
````
JSON.stringify('2	2')
""2\t2""
JSON.parse(JSON.stringify('2	2'))
"2	2"
JSON.stringify('2	2')
""2\t2""
JSON.parse('"2\t2"')
VM330:1 Uncaught SyntaxError: Unexpected number in JSON at position 2
    at JSON.parse (<anonymous>)
    at <anonymous>:1:6
````
问题好像有点眉头了，为什么直接使用字符串的时候会有问题呢？是不是js的字符串定义和解析的锅？[MDN上的js的string](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)
````
转义字符节
除了普通的可打印字符以外，一些特殊有特殊功能的字符可以通过转义字符的形式放入字符串中：

Code	Output
\0	空字符
\'	单引号
\"	双引号
\\	反斜杠
\n	换行
\r	回车
\v	垂直制表符
\t	水平制表符
\b	退格
\f	换页
\uXXXX	unicode 码
\u{X} ... \u{XXXXXX}	unicode codepoint 
\xXX	Latin-1 字符(x小写)
````
问题的根本原因在于使用`V8的JSON.parse`的时候，参数会被V8先解析为字符串；
````
'"\ttab"' =>
""	tab""
'"\nnewline"' =>
""
newline""
'"2\t2"' =>
""2	2""
````
被解析后的字符串变了样子，这些解析后的字符串不再符合JSON的格式，所以解析就会报错了。[详情参考：从一个 JSON.parse 错误深入研究 JavaScript 的转义字符](https://zhuanlan.zhihu.com/p/31030352)

那么如果想要正确的处理这些内容应该如何处理？`JSON.parse(String.raw`"2\t2"`)`，如下：
````
String.raw`"2\t2"`
""2\t2""
JSON.parse(String.raw`"2\t2"`)
"2	2"
````

#### number
数值（number）也与C或者Java的数值非常相似。除去未曾使用的八进制与十六进制格式。除去一些编码细节。

![clipboard.png](https://json.org/number.gif)


## javascript的JSON对象
>JSON对象包含两个方法: 用于解析 JavaScript Object Notation  (JSON) 的 parse() 方法，以及将对象/值转换为 JSON字符串的 stringify() 方法。除了这两个方法, JSON这个对象本身并没有其他作用，也不能被调用或者作为构造函数调用。
### parse
`JSON.parse() `方法用来解析JSON字符串，构造由字符串描述的JavaScript值或对象。提供可选的reviver函数用以在返回之前对所得到的对象执行变换(操作)。[parse语法介绍和使用mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)

    JSON.parse(text[, reviver])
    >如果指定了 reviver 函数，则解析出的 JavaScript 值（解析值）会经过一次转换后才将被最终返回（返回值）。更具体点讲就是：解析值本身以及它所包含的所有属性，会按照一定的顺序（从最最里层的属性开始，一级级往外，最终到达顶层，也就是解析值本身）分别的去调用 reviver 函数，在调用过程中，当前属性所属的对象会作为 this 值，当前属性名和属性值会分别作为第一个和第二个参数传入 reviver 中。如果 reviver 返回 undefined，则当前属性会从所属对象中删除，如果返回了其他值，则返回的值会成为当前属性新的属性值。

    >当遍历到最顶层的值（解析值）时，传入 reviver 函数的参数会是空字符串 ""（因为此时已经没有真正的属性）和当前的解析值（有可能已经被修改过了），当前的 this 值会是 {"": 修改过的解析值}，在编写 reviver 函数时，要注意到这个特例。（这个函数的遍历顺序依照：从最内层开始，按照层级顺序，依次向外遍历）
### stringify
`JSON.stringify()` 方法是将一个JavaScript值(对象或者数组)转换为一个 JSON字符串，如果指定了replacer是一个函数，则可以替换值，或者如果指定了replacer是一个数组，可选的仅包括指定的属性。[mdn文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
    JSON.stringify(value[, replacer [, space]])
需要注意后面连个参数，`replacer`可以是函数或者数组；
* 数组的值代表将被序列化成JSON字符串的属性名。
* 作为函数，它有两个参数，键(key)值(value)都会被序列化。

    >如果返回一个 Number, 转换成相应的字符串被添加入JSON字符串。
    如果返回一个 String, 该字符串作为属性值被添加入JSON。
    如果返回一个 Boolean, "true" 或者 "false"被作为属性值被添加入JSON字符串。
    如果返回任何其他对象，该对象递归地序列化成JSON字符串，对每个属性调用replacer方法。除非该对象是一个函数，这种情况将不会被序列化成JSON字符串。
    如果返回undefined，该属性值不会在JSON字符串中输出。
注意: 不能用replacer方法，从数组中移除值(values)，如若返回undefined或者一个函数，将会被null取代。

`space`参数用来控制结果字符串里面的间距。如果是一个数字, 则在字符串化时每一级别会比上一级别缩进多这个数字值的空格（最多10个空格）；如果是一个字符串，则每一级别会比上一级别多缩进用该字符串（或该字符串的前十个字符），可以使用`\t`制表符来进行数据的格式化。

## fast-json-stringify
最近发现一个库[fast-json-stringify](https://github.com/fastify/fast-json-stringify),因为他的介绍很简洁：`2x faster than JSON.stringify()`;需要一探究竟。