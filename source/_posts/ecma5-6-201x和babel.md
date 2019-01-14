---
title: ecma5|6|201x和babel
date: 2019-01-14 23:50:41
tags:
---
# ecma
* 中文名: 欧洲计算机制造联合会 
* 外文名: European Computer Manufactures Association  
* 地 区: 日内瓦 
* 缩 写: ECMA 
* 目 的: 信息处理和电信系统 
* 包 括: 程序语言和输入输出 
* 组 织: 国际标准化组织
>这个组织的目标是评估，开发和认可电信和计算机标准。大家决定把ECMA的总部设在日内瓦是因为这样能够让它与其它与之协同工作的标准制定组织更接近一些，比方说国际标准化组织（ISO）和国际电子技术协会（IEC）。ECMA是“European Computer Manufactures Association”的缩写，中文称欧洲计算机制造联合会。是1961年成立的旨在建立统一的电脑操作格式标准--包括程序语言和输入输出的组织。  
主要任务是研究信息和通讯技术方面的标准并发布有关技术报告。ECMA并不是官方机构，而是由主流厂商组成的，他们经常与其他国际组织进行合作。就象ECMA的章程中所说的那样，这个非盈利组织的目标是发展“标准和技术报告以便促进和标准化对信息处理和电信系统的使用过程。

<!-- more -->

目前已经可以确认的标准：**注：此名单仅部分收录**
````
ECMA-6 7位被编码的字符集（同一样 ISO/IEC 646/ITU-T T.50）
ECMA-13文件结构和标记磁带（最新ISO 1001）
ECMA-35 ISO/IEC 2022年 字符内码
ECMA-43 8位被编码的字符集（和一样ISO/IEC 4873）
ECMA-48 ANSI逃命代码 （和一样ISO/IEC 6429）
ECMA-58 8英寸 软盘
ECMA-59 8英寸 软盘
ECMA-66 5¼-英寸 软盘
ECMA-69 8英寸 软盘
ECMA-70 5¼ -英寸 软盘
ECMA-74信息技术和电信设备散发的空气传播的噪音的测量（和一样ISO 7779）
ECMA-78 5¼ -英寸 软盘
ECMA-94 8位编码字符集（同一样 ISO/IEC 8859-1， -2， -3和-4）
ECMA-99 5¼-英寸1.2兆字节 软盘 （也ISO 8630）
ECMA-100 3½-英寸 软盘 （也ISO 8660）
ECMA-107 文件分配表 (FAT）文件系统
ECMA-113 8位被编码的字符集，拉丁语或斯拉夫（同一样 ISO/IEC 8859-5）
ECMA-114 8位被编码的字符集，拉丁语或阿拉伯语（同一样 ISO/IEC 8859-6）
ECMA-118 8位被编码的字符集，拉丁语或希腊语（同一样 ISO/IEC 8859-7）
ECMA-119 CD-ROM 文件系统（以后被采取 ISO 9660:1988）
ECMA-121 8位被编码的字符集，拉丁语或希伯来语（同一样 ISO/IEC 8859-8）
ECMA-125 3½-英寸 软盘 （也ISO 9529）
ECMA-128 8位被编码的字符集（同一样 ISO/IEC 8859-9）
ECMA-130 CD-ROM 黄皮书
ECMA-139 4mm 数字资料存贮 （二氨基二苯基砜）弹药筒（和一样ISO/IEC 10777）
ECMA-144 8位被编码的字符集（同一样 ISO/IEC 8859-10）
ECMA-146 4mm DAT 数据插件（和一样ISO/IEC 11321）
ECMA-167 普遍盘格式
ECMA-168 ISO 9660 第3级（ISO/IEC 13490）
ECMA-182 周期性多余信息检验 多项CRC-64-ECMA-182
ECMA-234应用程序编程接口为 窗口3.1
ECMA-262 ECMAScript （规范化 脚本(script)语言）
ECMA-334 C#编程语言
ECMA-335 共同语言基础设施
ECMA-340 NFC标准ISO18092（ECMA免费版）
ECMA-352 NFC标准ISO21481（ECMA免费版）
ECMA-357 ECMAScript为XML (E4X)
ECMA-363 通用3D 文件格式
ECMA-365 通用媒体光盘（用于PSP）
ECMA-368 超宽物理和MAC层
ECMA-369 超宽MAC-PHY接口
ECMA-372 C++/CLI
ECMA-376 办公文档开放XML
ECMA-377 200GB的HVD插卡
ECMA-378 100GB的HVD-ROM光碟
````
而其中就有本文重点介绍的`ECMAscript`；

# ECMAscript/ES5/ES6/ES20XX
这里主要简单的介绍一下关于JavaScript的一些历史。
## ECMAscript
`ECMAscript`是基于`Netscape javaScript`的一种标准脚本语言。它也是一种基于对象的语言，通过DOM可以操作网页上的任何对象。可以增加、删除、移动或者改变对象。使得网页的交互性大大提高。[更多`ECMAscript`信息和历史](https://zh.wikipedia.org/wiki/ECMAScript)
>ECMAScript是一种由Ecma国际（前身为欧洲计算机制造商协会）通过ECMA-262标准化的脚本程序设计语言。这种语言在万维网上应用广泛，它往往被称为`JavaScript`或`JScript`，但实际上后两者是`ECMA-262`标准的实现和扩展。

`ECMA`的第39号技术专家委员会（Technical Committee 39，简称`TC3`9）负责制订`ECMAScript`标准，成员包括`Microsoft、Mozilla、Google`等大公司。`TC39`的总体考虑是，ES5与ES3基本保持兼容，较大的语法修正和新功能加入，将由JavaScript.next完成。

**了解更多TC39的进度可以关注[tc39ecma262--github](https://github.com/tc39/ecma262)，[tc39标准进度](https://github.com/tc39/proposals)**

## ECMAscript/ES历史
`ES` 是 `ECMAScript` 的缩写。每次看到 `ES` 后面跟着数字，是 `ECMAScript` 的不同版本。实际上一共有 9 个版本【截止时间2018年，2018-11月的部分达到`stage4`的提案已经作为`ES2019`的部分了】。我们来深入了解下：

1. `ES1`：1997 年 6 月  ——  
2. `ES2`：1998 年 6 月  ——  
3. `ES3`：1999 年 12月  ——  
4. `ES4`：未通过

这是前 4 个版本的 `ECMAScript`，这里简单过一下。仅仅让你知道前 3 个版本每年出一个，而第 4 个版本因为政治因素未通过。

* `ES5`：2009 年 12月：将近 10 年之后，ES5 在 2009 年发布。而下个版本的 ECMAScript 也花了 6 年才发布，【目前`ie8`还有部分标准没有实现】。

* `ES6/ES2015`：2015 年 6 月：也许困惑就是从这里开始的。你可以看到， ES6 和 ES2015 实际上是同一个东西。
    起先被推广的名字是 ES6 。然而组委会要求 ECMAScript 必须做到每年做一次更新。由此，这个版本被更名为 ES 2015，且每年都需要更新，并命名为当前年的后缀。
    >从 2009 年发布的 ES5 到 2015 年发布的 ES6 共经历了 6 年，语言的变化非常大，引入了很多新的语法和特性。为了避免剧烈的变动，从 ES7(2016) 开始，版本发布会变得更加频繁，每次的变动也更小。每年会发布一个新版本，其中包含所有已经完成的特性。
* `ES2016(ES7)`：2016 年 6 月：ECMAScript 的第 7 个版本。
* `ES2017(ES8)`：2017 年 6 月：ECMAScript 的第 8 个版本。
* `ES2018(ES9)`：2018 年 6 月：ECMAScript 的第 9 个版本。

**由此可以知道，往后应`scma`组委会要求，`TC39`每年都会提交一个版本，可能每年提交的版本修改并不会增加非常多，但还是需要时刻关注**

## stage【新增特性过程】
每一项新特性，要最终纳入 `ECMAScript` 规范中，`TC39` 拟定了一个处理过程，称为 `TC39 process`。其中共包含 5 个阶段，`Stage 0 ~ Stage 4`，[stage的每个特性所处阶段记录](https://github.com/tc39/proposals)--[stage英文文档介绍](https://tc39.github.io/process-document/)。

`TC39` 遵循的原则是：分阶段加入不同的语言特性。一旦提案成熟，`TC39` 会根据提案中的变动来更新规范。直到最近，`TC39` 依然依赖基于 `Microsoft Word` 的比较传统的工作流程。但 `ES3` 出来之后，为了使其达到规范，总共花了十年时间，几乎没有任何改变。之后，ES6 又花了四年才能实现。

自 `ES6` 出来之后，他们精简了提案的修订过程，以满足现代化开发的需求。新流程使用 `HTML `的超集来格式化提案。他们使用 `GitHub pull requests`，这有助于增加社区的参与，并且提出的提案数量也增加了。这个规范现在是一个 `living standard`，这意味着提案会更快，而且我们也不用等待新版本的规范出来。

新流程涉及五个不同的 `Stage`。一个提案越成熟，越有可能最终将其纳入规范。

1. `Stage 0: strawman`
    任何尚未提交作为正式提案的讨论、想法变更或者补充都被认为是第 0 阶段的“稻草人（strawman）”提案。
    >任何 `TC39` 成员，或者注册为 `TC39` 贡献者的会员，都可以提交 `Stage 0 `的提案。

2. Stage 1: proposal
    该阶段产生一个正式的提案。确定一个带头人来负责该提案，带头人(或者联合带头人)必须是 TC39 的成员。描述清楚要解决的问题，
    >解决方案中必须包含例子，`API(high level AP)` 以及关于相关的语义和算法。潜在问题也应该指出来，例如与其他特性的关系，实现它所面临的挑战。polyfill 和 demo 也是必要的。
3. Stage 2: draft
    `Stage 2` 的提案应提供规范初稿。此时，语言的实现者开始观察 `runtime` 的具体实现是否合理。该实现可以使用` polyfill` 的方式，以便使代码可在` runtime `中的行为负责规范的定义。`javascript `引擎的实现为提案提供了原生支持。或者可以 `Babel `这样的编译时编译器来支持。本草案与最终标准中包含的特性不会有太大差别。草案之后，原则上只接受增量修改。
    >草案中包含新增特性语法和语义的，尽可能的完善的形式说明，允许包含一些待办事项或者占位符。必须包含 2 个实验性的具体实现，其中一个可以是用转译器实现的，例如 Babel。
4. Stage 3: candidate
    `Stage 3` 提案是建议的候选提案。在这个高级阶段，规范的编辑人员和评审人员必须在最终规范上签字。`Stage 3` 的提案不会有太大的改变，在对外发布之前只是修正一些问题。规范文档必须是完整的，评审人和 ECMAScript 的编辑要在规范上签字。至少要有 2 个符合规范的具体实现。
5. Stage 4: finished
    最后，当规范的实现至少通过两个验收测试时，提案进入 `Stage 4`。进入 `Stage 4 `的提案将包含在 `ECMAScript` 的下一个修订版中。
    >通过 Test 262 的验收测试。有 2 个通过测试的实现，以获取使用过程中的重要实践经验。ECMAScript 的编辑必须规范上的签字。规格修订和调度

TC39 打算每年七月向 ECMA 大会提交一份规范批准。以下是生成新规格修订的大致时间表：
* 2月1日：候选草案被生成。
* 2月-3月：60 天时间进行投票决定草案的去留。
* 3月 TC39 会议：第 4 阶段的提案被纳入，最终的语义被批准，从 master 新建一个 spec 的分支版本。只有编辑性的改变才能被接受。
* 4月-6月：ECMA CC 和 ECMA GA 审查期。
* 7月：由 ECMA 大会批准新标准

可以知道，每一年度的版本其实在当年的7-8月份已经基本确定，然后开始下一年的进度。

也正是由于历史原因，有4点需要注意【以下截止时间2018-12，内容会被不断地调整】：
1. `ES5`的修改经历了10年，而其中很多都是我们前端经常用到的内容，`ES5`的修改内容可以参考：[Standard ECMA-262, 5.1 Edition / June 2011](https://www.ecma-international.org/ecma-262/5.1/)，由于标准都是向前兼容的，所以这个版本包含了`es3`之前的标准，[es5增加的标准列表](https://developer.mozilla.org/en-US/docs/tag/ECMAScript%205);
2. `ES6`也是经历了6年时间，在2015年发布的，其中又发生了很多的变化，查看`ES6`的标准：[es6标准](https://www.ecma-international.org/ecma-262/6.0/)，通用也是包含了`es5`和之前的标准，[es6增加的标准列表1](https://developer.mozilla.org/en-US/docs/tag/ECMAScript%202015?page=1)--[es6增加的标准列表1](https://developer.mozilla.org/en-US/docs/tag/ECMAScript%202015?page=2)；
3. 为了避免这种较大的改动，`TC39`将会在每年提交一个版本，这样的变化在2016年开始，而从2016~2018年中的已经完成到`stage4`的提案列表：[es2016+的已定标准变化](https://github.com/tc39/proposals/blob/master/finished-proposals.md)；
4. 而每一年的天的各个阶段会有变化，查看当前年份的有效天内容：[当前年份的活跃天stage3~1](https://github.com/tc39/proposals)；

标准的发布，各大厂商是需要时间去做实现的，所以如果开发人员想第一时间来体验新的提案带来的便捷，肯定是需要把提案中的内容通过类似`polyfill`的方式来使用，这就是本文另一个重点`babel`的作用。

# babel的出现

>Babel 是一个通用的多用途 JavaScript 编译器。通过 Babel 你可以使用（并创建）下一代的 JavaScript，以及下一代的 JavaScript 工具。


## babel简介
作为一种语言，`JavaScript` 在不断发展，新的标准／提案和新的特性层出不穷。 在得到广泛普及之前，`Babel` 能够让你提前（甚至数年）使用它们。

Babel 把用最新标准编写的 `JavaScript` 代码向下编译成可以在今天随处可用的版本。 这一过程叫做“源码到源码”编译， 也被称为转换编译（transpiling，是一个自造合成词，即转换＋编译。以下也简称为转译）。

例如，`Babel` 能够将新的 `ES2015` 箭头函数语法：
````
const square = n => n * n;
// 转译为：
const square = function square(n) {
  return n * n;
};
````
不过 `Babel` 的用途并不止于此，它支持语法扩展，能支持像 `React` 所用的 `JSX` 语法，同时还支持用于静态类型检查的流式语法`（Flow Syntax）`。

更重要的是，`Babel` 的一切都是简单的插件，谁都可以创建自己的插件，利用 `Babel` 的全部威力去做任何事情。

再进一步，`Babel` 自身被分解成了数个核心模块，任何人都可以利用它们来创建下一代的 `JavaScript` 工具。

## babel7使用
>由于 JavaScript 社区没有统一的构建工具、框架、平台等等，因此 Babel 正式集成了对所有主流工具的支持。 从 Gulp 到 Browserify，从 Ember 到 Meteor，不管你的环境设置如何，Babel 都有正式的集成支持。

鉴于babel的灵活性，除了配置主流工具`webpack`、`gulp`等使用，也可以独自使用；
1. CLI: 使用命令来进行编译
2. Require hook: 在需要编译的文件中引入`require("babel-register");`/`import "babel-register";`来编译当前文件。

不过二者都需要配合配置文件`.babelrc`使用；下面主要示范`CLI`的使用。

### Babel/CLI 
`BABEL`的 `CLI` 是一种在命令行下使用 `Babel `编译文件的简单方法。

让我们先全局安装它来学习基础知识。

    $ npm install -D babel-cli
我们可以这样来编译我们的第一个文件：

    $ ./node_modules/.bin/babel my-file.js
这将把编译后的结果直接输出至终端。使用 --out-file 或着 -o 可以将结果写入到指定的文件。.
````
$ ./node_modules/.bin/babel example.js --out-file compiled.js
# 或
$ ./node_modules/.bin/babel example.js -o compiled.js
````
如果我们想要把一个目录整个编译成一个新的目录，可以使用 --out-dir 或者 -d。.
````
$ ./node_modules/.bin/babel src --out-dir lib
# 或
$ ./node_modules/.bin/babel src -d lib
````
一般而言，编译会被放在`package.json`的`scripts`字段当中，
````
"scripts": {
    "test": "./node_modules/mocha/bin/mocha",
    "build": "./node_modules/.bin/babel src -d lib",
    "prepublish": "npm run build"
  },
````
### 配置文件`.babelrc`
````
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "browsers": [
            "last 2 versions",
            "safari >= 7"
          ],
          "chrome": 52,
          "node": "6.10.0"
        },
        "modules": "commonjs",
        "useBuiltIns": "usage"
      }
    ]
  ],
  "plugins": [
    "@babel/plugin-syntax-dynamic-import",
    "@babel/plugin-syntax-import-meta",
    "@babel/plugin-proposal-class-properties",
    "@babel/plugin-proposal-nullish-coalescing-operator",
    "@babel/plugin-proposal-do-expressions"
  ]
}

````
#### plugins
[plugins](https://babeljs.io/docs/en/plugins)是`babel`实现`es2015+`的新的语法，而每实现一种语法的编译，都是一个`plugins`，

实例：`@babel/plugin-transform-arrow-functions`可以实现下面这个编译过程。
````
const square = n => n * n;
// 转译为：
const square = function square(n) {
  return n * n;
};
````
目前的`plugins`的集合目前可以查看：[babel的plugins集合](https://babeljs.io/docs/en/plugins)
#### @babel/preset-env
>Sidenote, if no targets are specified, @babel/preset-env behaves exactly the same as @babel/preset-es2015, @babel/preset-es2016 and @babel/preset-es2017 together (or the deprecated babel-preset-latest).

可以知道，`@babel/preset-env`是完成了`stage4`以内的所有语法，同时添加了更多的配置项；
````
"presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "browsers": [
            "last 2 versions",
            "safari >= 7"
          ],
          "chrome": 52,
          "node": "6.10.0"
        },
        "modules": "commonjs",
        "useBuiltIns": "usage"
      }
    ]
  ],
````
更多的详细信息查看：[babel的预设使用](https://babeljs.io/docs/en/babel-preset-env)

比较重要的是三个地方：
1. `targets`字段：适配所包含的运行环境，根据环境进行打包。
2. `modules`打包后的模块：
3. `useBuiltIns`是`polyfill`的引入方式，
    "usage" | "entry" | false, defaults to false.
#### polyfill
`Babel` 是处于构建时（也就是传统Java等语言的编译时），转译出来的结果在默认情况下并不包括 ES6 对运行时的扩展，例如，`builtins（内建，包括 Promise、Set、Map 等）`、内建类型上的原型扩展（如 `ES6 对 Array、Object、String 等内建类型原型上的扩展`）以及`Regenerator`（用于`generators / yield`）等都不包括在内。

需要配合`useBuiltIns`字段来使用。
1. "useBuiltIns": false
    不在代码中使用polyfills，表现形式和@babel/preset-latest一样，当使用ES6+语法及API时，在不支持的环境下会报错。
2. "useBuiltIns":"entry"
    在项目入口引入一次（多次引入会报错）
    ````
    import "@babel/polyfill"
    // or
    require("@babel/polyfill")
    ````
    插件@babel/preset-env会将把@babel/polyfill根据实际需求打散，只留下必须的，例如：
    ````
    // input file
    import "@babel/polyfill";
    ````
    ````
    // Out (different based on environment)
    import "core-js/modules/es6.promise";
    import "core-js/modules/es7.string.pad-start";
    import "core-js/modules/es7.string.pad-end";
    import "core-js/modules/es7.array.includes";
    ```` 
3. "useBuiltIns":"usage"
    在文件需要的位置单独按需引入，可以保证在每个bundler中只引入一份。例如：
    ````
    // In
    //a.js
    var a = new Promise();
    
    //b.js
    var b = new Map();
    ````
    ````
    // Out (if environment doesn't support it)
    import "core-js/modules/es6.promise";
    var a = new Promise();
    
    import "core-js/modules/es6.map";
    var b = new Map();
    
    // Out (if environment supports it)
    var a = new Promise();
    var b = new Map();
    ````
####  

## babel7之前的一些历史
[bale7发布](https://babeljs.io/blog/2018/08/27/7.0.0)；可以说是对以前改变了不少，最为主要的是以下两点：
### babel-preset-es20xx
[babel-preset-es2015](https://babeljs.io/docs/en/babel-preset-es2015)、[babel-preset-es2016](https://babeljs.io/docs/en/babel-preset-es2016)、[babel-preset-es2017](https://babeljs.io/docs/en/babel-preset-es2017)是`babel`配合`ECMA`组委会给`TC39`的要求，希望`ECMA-262`能够每年都更新一个版本，所以每年发了一个版本的话，那么`babel`就会把新的语法创建一个新的预设，以年份命名。

这样看似很好的，不过有一个问题，随着每年的推进，开发者需要每一年就在配置文件中添加一个当年的预设，所以把这些作为`stage4`以内的完成的ECMA-261提案集合到一个预设`@babel/preset-env`;而babel只要不停地更新这个预设就可以了。

### stage的移除
之前提到了，关于`TC39`的提案进程，有的时候除了`stage4`的提案外，开发者还想使用其他的进度上的提案，`babel`使用了[babel-preset-stage-0](https://babeljs.io/docs/en/babel-preset-stage-0)、[babel-preset-stage-1](https://babeljs.io/docs/en/babel-preset-stage-1)、[babel-preset-stage-2](https://babeljs.io/docs/en/babel-preset-stage-2)、[babel-preset-stage-3](https://babeljs.io/docs/en/babel-preset-stage-3)这四个预设分别对应四个阶段，

做法看似很好，同样有一个隐患，很多时候，开发者只想使用某一个阶段的提案，而不是把这个阶段的所有提案都引进来，所以`babel7`的做法是把这个`stage-x`的预设全都废弃，而是让开发者来进行自己手动将需要的对应阶段的插件引入即可，而不用采用预设的方案，因为预设就是插件的集合。

参考：
[es简介译文](https://www.zcfy.cc/article/javascript-wtf-is-es6-es8-es-2017-ecmascript-4130.html?t=new)
[es个版本的增加项](https://www.imooc.com/article/37899)
[es2015的](https://developer.mozilla.org/en-US/docs/tag/ECMAScript%205)
[es6增加](https://www.ecma-international.org/ecma-262/6.0/)
[es5增加](https://www.ecma-international.org/ecma-262/5.1/)
[es和ecmascript关系](https://codeburst.io/javascript-wtf-is-es6-es8-es-2017-ecmascript-dca859e4821c)
[babel预设](https://babeljs.io/docs/en/next/babel-preset-env.html)
[babel翻译文档](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/user-handbook.md)
[babel使用实例](https://www.jianshu.com/p/0dc3bddb6da8)
[tc39的进程理解](http://esnext.justjavac.com/news/2018/02/08/tc39-process-document/)
[plugins必知插件](https://www.jianshu.com/p/0dc3bddb6da8)