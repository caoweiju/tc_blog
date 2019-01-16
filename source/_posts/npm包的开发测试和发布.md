---
title: npm包的开发测试和发布
date: 2019-01-16 23:49:55
tags:
- npm
- node
- mocha
- babel
categories: 
- JavaScript
---
# npm包管理
`npm`其实是`Node.js`的包管理工具（`node package manager`）。

为啥我们需要一个包管理工具呢？因为我们在`Node.js`上开发时，会用到很多别人写的`JavaScript`代码。如果我们要使用别人写的某个包，每次都根据名称搜索一下官方网站，下载代码，解压，再使用，非常繁琐。于是一个集中管理的工具应运而生：大家都把自己开发的模块打包后放到`npm`官网上，如果要使用，直接通过npm安装就可以直接用，不用管代码存在哪，应该从哪下载。

更重要的是，如果我们要使用模块A，而模块A又依赖于模块B，模块B又依赖于模块C和模块D，`npm`可以根据依赖关系，把所有依赖的包都下载下来并管理起来。否则，靠我们自己手动管理，肯定既麻烦又容易出错。

<!-- more -->

## npm的基础使用
`npm`的指令其实常用的并不多[官方文档](https://docs.npmjs.com/cli-documentation/cli)；列出来如下面：
* `access` 
    Set access level on published packages
* `adduser`
        Add a registry user account
* `audit` 
        Run a security audit
* `bin` 
        Display npm bin folder
* `bugs `
        Bugs for a package in a web browser maybe
* `build`
        Build a package
* `bundle` 
        REMOVED *已删除*
* `cache`  
        Manipulates packages cache
* `ci`  
        Install a project with a clean slate
* `completion ` 
        Tab Completion for npm
* `config`  
        Manage the npm configuration files
* `dedupe`  
        Reduce duplication
* `deprecate`  
        Deprecate a version of a package
* `dist-tag`  
        Modify package distribution tags
* `docs`  
        Docs for a package in a web browser maybe
* `doctor`  
        Check your environments
* `edit`  
        Edit an installed package
* `explore`  
        Browse an installed package
* `help-search`  
        Search npm help documentation
* `help`  
        Get help on npm
* `hook`  
        Manage registry hooks
* `init`  
        create a package.json file
* `install-ci-test`  
        Install a project with a clean slate and run tests
* `install-test`  
        Install package(s) and run tests
* `install`  
        Install a package
* `link`  
        Symlink a package folder
* `logout ` 
        Log out of the registry
* `ls`  
        List installed packages
* `npm ` 
        javascript package manager
* `outdated`  
        Check for outdated packages
* `owner ` 
        Manage package owners
* `pack`  
        Create a tarball from a package
* `ping`  
        Ping npm registry
* `prefix`  
        Display prefix
* `profile`  
        Change settings on your registry profile
* `prune`  
        Remove extraneous packages
* `publish ` 
        Publish a package
* `rebuild`  
        Rebuild a package
* `repo ` 
        Open package repository page in the browser
* `restart`  
        Restart a package
* `root ` 
        Display npm root
* `run-script`  
        Run arbitrary package scripts
* `search ` 
        Search for packages
* shrinkwrap  
        Lock down dependency versions for publication
* `star`  
        Mark your favorite packages
* `stars`  
        View packages marked as favorites
* `start`  
        Start a package
* `stop`  
        Stop a package
* `team`  
        Manage organization teams and team memberships
* `test`  
        Test a package
* `token`  
        Manage your authentication tokens
* `uninstall`  
        Remove a package
* `unpublish`  
        Remove a package from the registry
* `update`  
        Update a package
* `version ` 
        Bump a package version
* `view`  
        View registry info
* `whoami`  
        Display npm username



### init
初始化创建package.json
>npm init [--force|-f|--yes|-y|--scope]
npm init <@scope> (same as `npx <@scope>/create`)
npm init [<@scope>/]<name> (same as `npx [<@scope>/]create-<name>`)

### search
搜索查看远程`npm`相关资源包信息
>npm search [-l|--long] [--json] [--parseable] [--no-description] [search terms ...]
aliases: s, se, find

### install
可以是说是`install`是最为常见的命令[官方介绍](https://docs.npmjs.com/cli/install)，
>npm install (with no args, in package dir)
npm install [<@scope>/]<name>
npm install [<@scope>/]<name>@<tag>
npm install [<@scope>/]<name>@<version>
npm install [<@scope>/]<name>@<version range>
npm install <git-host>:<git-user>/<repo-name>
npm install <git repo url>
npm install <tarball file>
npm install <tarball url>
npm install <folder>  

>alias: npm i
common options: [-P|--save-prod|-D|--save-dev|-O|--save-optional] [-E|--save-exact] [-B|--save-bundle] [--no-save] [--dry-run]  

>In global mode (ie, with -g or --global appended to the command), it installs the current package context (ie, the current working directory) as a global package. The -g or --global argument will cause npm to install the package globally rather than locally.  

>The -f or --force argument will force npm to fetch remote resources even if a local copy exists on disk.

上面的还介绍已经很详细了，所以这里只是讲一下`npm install packageName [|--save |--save-prod|--save-dev]`的区别；
* npm install babel 
    [npm5以前](https://github.com/npm/npm/releases/tag/v5.0.0),会把X包安装到node_modules目录中，不会修改package.json的dependencies字段，之后运行npm install命令时，不会自动安装X
* npm install babel
    [npm5以后](https://github.com/npm/npm/releases/tag/v5.0.0),会把X包安装到node_modules目录中，会修改package.json的dependencies字段，之后运行npm install命令时，会自动安装X, 线上环境时会被安装
* npm install babel -P 
    -P, --save-prod: Package will appear in your dependencies. This is the default unless -D or -O are present. Package will appear in your dependencies, With the --production flag (or when the NODE_ENV environment variable is set to production), npm will install modules listed in dependencies.
* npm install babel -D
    Package will appear in your devDependencies，With the --production flag (or when the NODE_ENV environment variable is set to production), npm will not install modules listed in devDependencies. 会把X包安装到node_modules目录中，会在package.json的devDependencies属性下添加X，之后运行npm install命令时，会自动安装X到node_modules目录中，之后运行npm install –production或者注明NODE_ENV变量值为production时，不会自动安装X到node_modules目录中

### update
升级某个资源包或者全部资源包到某一个版本或者匹配的最新版本。
>npm update [-g] [<pkg>...]
aliases: up, upgrade

### uninstall
移除某个资源包
>npm uninstall [<@scope>/]<pkg>[@<version>]... [-S|--save|-D|--save-dev|-O|--save-optional|--no-save]
aliases: remove, rm, r, un, unlink


## npm包创建、编写、测试、维护
`Node`出现之前，`JavaScript`是缺少包结构的。`CommonJS`致力于改变这种现状，于是定义了包的结构规范。而`NPM`的出现则是为了在`CommonJS`规范的基础上，实现解决包的安装卸载，依赖管理，版本管理等问题。`require`的查找机制明了之后，我们来看一下包的细节。
一个符合`CommonJS`规范的包应该是如下这种结构：

- 一个`package.json`文件应该存在于包顶级目录下

- 二进制文件应该包含在`bin`目录下(可选)

- `JavaScript`代码入库是`index.js`，其他包含在`lib`目录下

- 文档应该在`doc`目录下(可选)

- 单元测试应该在`test`目录下(可选)

### 初始化包
1. 创建包的根目录
    ````
    mkdir testpackage
    ````
2. 初始化
    ````
    npm init   // 需要进行一些基本配置
    ````
### 编写
1. 创建入口文件
    ````
    touch index.js
    ````
2. 编写代码
    ````
    const updateQueryString = function(url, key, value) {
        let urlParts = url.split('#'),
            hash = '',
            uri = urlParts.shift(),
            re = new RegExp(`([?&])${key}=.*?(&|$)`, 'i'),
            separator = uri.indexOf('?') !== -1 ? '&' : '?',
            encodeKey = encodeURIComponent(key),
            encodeValue = encodeURIComponent(value);
    
        urlParts.length > 0 && (hash = `#${urlParts.join('#')}`);
    
        if (uri.match(re)) {
            return uri.replace(re, `$1${encodeKey}=${encodeValue}$2`) + hash;
        } else {
            return `${uri}${separator}${encodeKey}=${encodeValue}${hash}`;
        }
    };
    
    // 最后的导出部分
    module.exports = {
        updateQueryString
    };
    ````
3. 测试
    1. 创建包的根目录
        ````
        npm i mocha -D    // 安装测试库
        npm i chai -D     // 安装断言库
        mkdir test
        cd test
        touch index.test.js
        ````
    2. 编写测试代码
        ````
        const utils = require('./../index.js');
        const expect = require('chai').expect;
        
        let {
            updateQueryString
        } = utils;
        
        describe('updateQueryString函数的测试', function() {
            it('https://test.com/path?test=11 修改test参数为22 应该等于 https://test.com/path?test=22', function() {
                expect(updateQueryString('https://test.com/path?test=11', 'test', 22)).to.be.equal('https://test.com/path?test=22');
            });
        });
        ````
    3. 运行测试
        ````
        cd ..
        ./node_modules/mocha/bin/mocha 
        ````

## npm包的发布
1. 注册账号[npm官网](https://www.npmjs.com/)
2. 终端执行 `npm login`,输入用户名和密码 、邮箱
3. `npm publish` 发布

## Organization包
我们经常可以看到`@angular`、`@ionic`他们的包， 都可以以@开头，那么我们的可不可以，原来`angular、ionic`都属于一个组织（`Organization`）只有新创建一个`Organization`组织之后，才能创建`@testorg/testpackname`这样的包！！！

那么我们就可以去官网上创建我们的`Organization`，命名之后，[官方步骤](https://docs.npmjs.com/creating-and-publishing-an-org-scoped-package)，

1. 初始化
    ````
    npm init --scope=<your_org_name>
    ````
    >npm init foo -> npx create-foo
    npm init @usr/foo -> npx @usr/create-foo
    npm init @usr -> npx @usr/create
2. 修改`package.json`里面的`name`字段为`@your_org_name/<pkg_name>`
3. 发布
    ````
    npm publish --access public  // 公开包发布
    ````

## npm包支持esmodule
使用babel来进行一些现代JavaScript的支持，
1. 创建配置文件
    ````
    touch .babelrc
    ````
2. 安装先关包
3. 配置babel
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
        "@babel/plugin-proposal-json-strings",
        [
          "@babel/plugin-proposal-decorators",
          {
            "legacy": true
          }
        ],
        "@babel/plugin-proposal-function-sent",
        "@babel/plugin-proposal-export-namespace-from",
        "@babel/plugin-proposal-numeric-separator",
        "@babel/plugin-proposal-throw-expressions",
        "@babel/plugin-proposal-export-default-from",
        "@babel/plugin-proposal-logical-assignment-operators",
        "@babel/plugin-proposal-optional-chaining",
        [
          "@babel/plugin-proposal-pipeline-operator",
          {
            "proposal": "minimal"
          }
        ],
        "@babel/plugin-proposal-nullish-coalescing-operator",
        "@babel/plugin-proposal-do-expressions"
      ]
    }
    
    ````
4. 编译
    ````
    ./node_modules/.bin/babel src -d lib
    ````
最后的测试代码地址[test-demo-npm](https://github.com/caoweiju/js_com_utils)
