---
title: npm和yarn的版本lock机制的理解
date: 2019-01-18 22:53:17
tags:
- lock
- 版本控制
categories: 
- npm
- yarn
---
# node包管理
>包是一段可以复用的代码，这段代码可以从全局注册表下载到开发者的本地环境。每个包可能会，也可能不会依赖于别的包。简单地说，包管理器是一段代码，它可以让你管理依赖（你或者他人写的外部代码），你的项目需要这些依赖来正确运行。

为啥我们需要一个包管理工具呢？因为我们在`Node.js`上开发时，会用到很多别人写的`JavaScript`代码。如果我们要使用别人写的某个包，每次都根据名称搜索一下官方网站，下载代码，解压，再使用，非常繁琐。

更重要的是，如果我们要使用模块A，而模块A又依赖于模块B，模块B又依赖于模块C和模块D，`npm`可以根据依赖关系，把所有依赖的包都下载下来并管理起来。否则，靠我们自己手动管理，肯定既麻烦又容易出错。

<!-- more -->

于是一个集中管理的工具应运而生：
1. 大家都把自己开发的模块打包后放到`npm`官网上，如果要使用，直接通过`npm`安装就可以直接用，不用管代码存在哪，应该从哪下载。
2. `Yarn` 是为了弥补`npm` 的一些缺陷[速度慢，稳定性高]而出现的。”
## npm
>`npm` 为你和你的团队打开了连接整个 `JavaScript` 天才世界的一扇大门。它是世界上最大的软件注册表，每星期大约有 30 亿次的下载量，包含超过 600000 个 包（`package`） （即，代码模块）。来自各大洲的开源软件开发者使用 `npm` 互相分享和借鉴。包的结构使您能够轻松跟踪依赖项和版本。

下面是关于 `npm` 的快速介绍：`npm` 由三个独立的部分组成：
* 网站
    网站 是开发者查找包（`package`）、设置参数以及管理 `npm` 使用体验的主要途径。
* 注册表（`registry`）
    注册表 是一个巨大的数据库，保存了每个包（`package`）的信息。
* 命令行工具 (`CLI`)
    `CLI` 通过命令行或终端运行。开发者通过 CLI 与 npm 打交道。

## yarn
`Yarn`发布于2016年10月，并在`Github`上迅速拥有了2.4万个Star。而`npm`只有1.2万个`star`。这个项目由一些高级开发人员维护，包括了`Sebastian McKenzie`（Babel.js）和`Yehuda Katz`（Ember.js、Rust、Bundler等）。

>`Yarn`一开始的主要目标是解决上一节中描述的由于语义版本控制而导致的npm安装的不确定性问题。虽然可以使用`npm shrinkwrap`来实现可预测的依赖关系树，但它并不是默认选项，而是取决于所有的开发人员知道并且启用这个选项。
Yarn采取了不同的做法。每个`yarn`安装都会生成一个类似于`npm-shrinkwrap.json`的`yarn.lock`文件，而且它是默认创建的。除了常规信息之外，`yarn.lock`文件还包含要安装的内容的校验和，以确保使用的库的版本相同。

yarn的优化主要体现在：
1. 速度快 ：
    * 并行安装：无论 npm 还是 Yarn 在执行包的安装时，都会执行一系列任务。npm 是按照队列执行每个 package，也就是说必须要等到当前 package 安装完成之后，才能继续后面的安装。而 Yarn 是同步执行所有任务，提高了性能。
    * 离线模式：如果之前已经安装过一个软件包，用Yarn再次安装时之间从缓存中获取，就不用像npm那样再从网络下载了。
2. 安装版本统一：为了防止拉取到不同的版本，`Yarn` 有一个锁定文件 (lock file) 记录了被确切安装上的模块的版本号。每次只要新增了一个模块，Yarn 就会创建（或更新）`yarn.lock` 这个文件。这么做就保证了，每一次拉取同一个项目依赖时，使用的都是一样的模块版本。
3. 更好的语义化： `yarn`改变了一些`npm`命令的名称，比如 `yarn add/remove`，感觉上比 npm 原本的 `install/uninstall` 要更清晰。

## node包的安装
1. 执行工程自身 `preinstall`
    * 当前 npm 工程如果定义了` preinstall` 钩子此时会被执行。
2. 确定首层依赖 
    * 模块首先需要做的是确定工程中的首层依赖，也就是 `dependencies` 和 `devDependencies` 属性中直接指定的模块（假设此时没有添加 `npm install` 参数）。工程本身是整棵依赖树的根节点，每个首层依赖模块都是根节点下面的一棵子树，npm 会开启多进程从每个首层依赖模块开始逐步寻找更深层级的节点。
3. 获取模块
    * 获取模块是一个递归的过程，分为以下几步：
    * 获取模块信息。在下载一个模块之前，首先要确定其版本，这是因为 `package.json` 中往往是 `semantic version`（semver，语义化版本）。此时如果版本描述文件（`npm-shrinkwrap.json` 或 `package-lock.json`）中有该模块信息直接拿即可，如果没有则从仓库获取。如` packaeg.json` 中某个包的版本是 `^1.1.0，npm` 就会去仓库中获取符合 `1.x.x` 形式的最新版本。
    * 获取模块实体。上一步会获取到模块的压缩包地址（`resolved` 字段），npm 会用此地址检查本地缓存，缓存中有就直接拿，如果没有则从仓库下载。
    * 查找该模块依赖，如果有依赖则回到第1步，如果没有则停止。
4. 模块扁平化（`dedupe`）
    * 上一步获取到的是一棵完整的依赖树，其中可能包含大量重复模块。比如 A 模块依赖于 `loadsh`，B 模块同样依赖于 `lodash`。在 npm3 以前会严格按照依赖树的结构进行安装，因此会造成模块冗余。`yarn`和从 `npm5` 开始默认加入了一个 `dedupe` 的过程。它会遍历所有节点，逐个将模块放在根节点下面，也就是` node-modules` 的第一层。当发现有重复模块时，则将其丢弃。这里需要对重复模块进行一个定义，它指的是模块名相同且 `semver` 兼容。每个 `semver` 都对应一段版本允许范围，如果两个模块的版本允许范围存在交集，那么就可以得到一个兼容版本，而不必版本号完全一致，这可以使更多冗余模块在 `dedupe` 过程中被去掉。
5. 安装模块
    * 这一步将会更新工程中的`node_modules`，并执行模块中的生命周期函数（按照 `preinstall、install、postinstall` 的顺序）。
6. 执行工程自身生命周期
    * 当前 npm 工程如果定义了钩子此时会被执行（按照 `install、postinstall、prepublish、prepare` 的顺序）。

## 包依赖关系
我们要使用模块A，而模块A又依赖于模块B，模块B又依赖于模块C和模块D，npm可以根据依赖关系，把所有依赖的包都下载下来并管理起来，而这种依赖又有不一样的表现形式。
1. 嵌套依赖
2. 扁平依赖
### 嵌套依赖

假设目前工程依赖 A, B, C 三个库，而他们对某个库 `somelib` 存在这样的依赖关系：
````
A - somelib 1.4.x
B - somelib 1.6.x
C - somelib 1.6.x
````
如果要安装 ABC 三个库，那么 `somelib` 会存在版本冲突。`npm5+/yarn` 会在实际安装时，给三个库分别下载各自依赖的 `somelib` 版本。假设 npm 先安装了 A, 由于 A 依赖` somelib 1.4.x` 版本，那么 自身依赖先安装`1.4.x `。再安装 B, C 时，由于 B, C 依赖的都不是` 1.4.x`, 于是安装完之后，关系就变成这个样子了：
````
node_modules
├── A
│   └── node_modules
│       └── somelib 1.4.x
├── B
│   └── node_modules
│       └── somelib 1.6.x
└── C
    └── node_modules
        └── somelib 1.6.x
````
这样就是嵌套依赖。
很显然这种方式很大的浪费了磁盘空间。

### 扁平依赖
当关联依赖中包括对某个软件包的重复引用，在实际安装时将尽量避免重复的创建。

假设目前工程依赖 A, B, C 三个库，而他们对某个库 `somelib` 存在这样的依赖关系：
````
A - somelib 1.4.x
B - somelib 1.6.x
C - somelib 1.6.x
````
如果要安装 ABC 三个库，那么 `somelib` 会存在版本冲突。`npm5+/yarn` 会在实际安装时，给三个库分别下载各自依赖的 `somelib` 版本。假设 npm 先安装了 A, 由于 A 依赖` somelib 1.4.x` 版本，那么 `1.4.x `会变成主版本。再安装 B, C 时，由于 B, C 依赖的都不是` 1.4.x`, 于是安装完之后，关系就变成这个样子了：
````
node_modules
├── A
├── somelib 1.4.x
├── B
│   └── node_modules
│       └── somelib 1.6.x
└── C
    └── node_modules
        └── somelib 1.6.x
````
这样就是扁平依赖。
>需要注意的是，明明 B, C 都依赖 1.6.x 版本，实际上 `npm5+/yarn` 却要把这个版本保存两次，这样明显是对磁盘空间的浪费。我们把这种情况就称为不完全扁平的。目前这种情况还无法安全解决。

## lock文件
>锁文件是由包管理器自动生成的。它包含了重现全部的依赖源码树需要的所有信息、你的项目依赖中的所有信息，以及它们各自的版本。

现在值得强调的是，`Yarn` 使用了锁文件，而` npm5`以前没有默认锁文件，`npm5`之后加入了默认锁文件功能。我们会谈到这种差别导致的一些后果。既然我已经向你介绍了包管理器这部分，现在我们来讨论依赖本身。

目前常见的两种lock文件：
1. `packahe-lock.json` 是npm5之后默认生成的锁文件
2. `yarn.lock` 是yarn的锁文件

### packahe-lock.json解析

````
{
  "name": "package-name",
  "version": "1.0.0",
  "lockfileVersion": 1,
  "dependencies": {
    "cacache": {
      "version": "9.2.6",
      "resolved": "https://registry.npmjs.org/cacache/-/cacache-9.2.6.tgz",
      "integrity": "sha512-YK0Z5Np5t755edPL6gfdCeGxtU0rcW/DBhYhYVDckT+7AFkCCtedf2zru5NRbBLFk6e7Agi/RaqTOAfiaipUfg=="
    },
    "duplexify": {
      "version": "3.5.0",
      "resolved": "https://registry.npmjs.org/duplexify/-/duplexify-3.5.0.tgz",
      "integrity": "sha1-GqdzAC4VeEV+nZ1KULDMquvL1gQ=",
      "dependencies": {
        "end-of-stream": {
          "version": "1.0.0",
          "resolved": "https://registry.npmjs.org/end-of-stream/-/end-of-stream-1.0.0.tgz",
          "integrity": "sha1-1FlucCc0qT5A6a+GQxnqvZn/Lw4="
        },
    }
  }
}
````
可以看出来，package-lock.json把所有的包的依赖顺序列出来，第一次出现的包名会提升到顶层，后面重复出现的将会放入被依赖包的node_modules当中。引起不完全扁平化问题。
### yarn.lock解析

````
# THIS IS AN AUTOGENERATED FILE. DO NOT EDIT THIS FILE DIRECTLY.
# yarn lockfile v1
package-1@^1.0.0:
  version "1.0.3"
  resolved "https://registry.npmjs.org/package-1/-/package-1-1.0.3.tgz#a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
package-2@^2.0.0:
  version "2.0.1"
  resolved "https://registry.npmjs.org/package-2/-/package-2-2.0.1.tgz#a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
  dependencies:
    package-4 "^4.0.0"
package-3@^3.0.0:
  version "3.1.9"
  resolved "https://registry.npmjs.org/package-3/-/package-3-3.1.9.tgz#a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
  dependencies:
    package-4 "^4.5.0"
package-4@^4.0.0, package-4@^4.5.0:
  version "4.6.3"
  resolved "https://registry.npmjs.org/package-4/-/package-4-2.6.3.tgz#a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
````
显然yarn.lock锁文件把所有的依赖包都扁平化的展示了出来，对于同名包但是semver不兼容的作为不同的字段放在了yarn.lock的同一级结构中。

## 验证实例
在一个测试package工程里面，安装了以下三个包，安装了react-router 3.2.1，另外安装了react-router-dom 4.3.1 和react-router-native 4.3.0，这两个都依赖"react-router": "^4.3.0"，结果如下：
.
└── node_modules
    ├── react-router-dom4.3.1
    │   └── react-router4.3.1
    ├── react-router-native4.3.0
    │   └── react-router4.3.1
    └── react-router3.2.1


查看package-lock.json结果和最后node_modules安装结果：



[lock官方介绍](https://yarnpkg.com/blog/2017/05/31/determinism/)
[理解 NPM 5 中的 lock 文件](https://juejin.im/post/5943849aac502e006b84ce07 )
[知乎问答1](https://zhuanlan.zhihu.com/p/24045251)
[知乎问答2](https://www.zhihu.com/question/51502849/answer/126290224)


  [1]: /img/bVbjObA
  [2]: /img/bVbjObP
  [3]: /img/bVbjObV