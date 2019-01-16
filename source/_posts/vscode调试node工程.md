---
title: vscode调试node工程
date: 2019-01-16 23:58:17
tags:
- vscode
- node
- debug
categories: 
- tool
---
# node调试
关于[node调试](http://nodejs.cn/api/debugger.html)，Node.js 包含一个进程外的调试工具，可以通过V8检查器与内置的调试客户端访问。 要使用它，需要以 inspect 参数启动 Node.js，并带上需要调试的脚本的路径；然后会出现一个提示，表明已成功启动调试器，
````
$ node inspect myscript.js
< Debugger listening on ws://127.0.0.1:9229/80e7a814-7cd3-49fb-921a-2e02228cd5ba
< For help see https://nodejs.org/en/docs/inspector
< Debugger attached.
Break on start in myscript.js:1
> 1 (function (exports, require, module, __filename, __dirname) { global.x = 5;
  2 setTimeout(() => {
  3   console.log('world');
debug>
````

`vscode`也是使用了`node`的自带功能，不过不需要在命令来一步一步的调试，这个`node`提供的调试有点和linux上的gdb调试器类似，使用起来并不是很顺手，所以使用ide还是不错的选择。

<!-- more -->

## vscode调试
主要是vscode的配置，至于调试的一些使用功能【查看变量、断点等】，来ide里面一段倒腾就明白了，配置的话是一个`launch.json`来配置的，如何打开这个文件，以下两种方法：
* 菜单栏的`调试`~`打开设置`
* 左侧栏有个虫子按钮，点击，上边就有个齿轮，可以打开 `launch.json`

可以使用`node`来直接运行调试，也可以使用`npm`来运行`package.json`的`scripts`脚本；
使用`node`来调试，就像是运行 `node index.js`一样:
````
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch Program",
            "program": "${workspaceRoot}\\index.js" //linux mac是："${workspaceRoot}/index.js"
        }
    ]
}
````
使用`npm`来调试，就像是运行 `npm run dev`一样,需要注意你的`package.json`的`scripts`脚本:
````
//package.json
{
    "name": "debug",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "dev": "node index.js"
    },
    "author": "cwj",
    "license": "ISC"
}
//launch.json
{
    "version": "0.2.0",
    "configurations": [
         {
             "name": "Launch via NPM",
             "type": "node",
             "request": "launch",
             "cwd": "${workspaceFolder}",
             "runtimeExecutable": "npm",
             "runtimeArgs": [
                 "run-script", "dev"
             ],
             "port": 9229
        }
        
    ]
}
````

*有时候node使用了某个端口，停止后再次启动时经常遇到端口被占用*
````
//lsof -i :port 查看端口被占用进程的pid等信息
lsof -i :8888
//kill -9 pid  根据pid来删除使用node需要的端口的进程 
kill -9 23456
````