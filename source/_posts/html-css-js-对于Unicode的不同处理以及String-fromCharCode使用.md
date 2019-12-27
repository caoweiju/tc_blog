---
title: html-css-js 对于Unicode的不同处理以及String.fromCharCode使用
date: 2019-12-27 18:55:02
tags:
- fromCharCode
categories: 
- HTML
---

## Unicode字符在前端的表现
对于Unicode字符，HTML、CSS、JS的计算方式完全不一样，但可以相互转换。

1. HTML字符计算方式  
对于Unicode，HTML采用十、十六进制方式计算，并以`&#{unicode};`方式进行表示，例如对于`|`字符，HTML的表示方式如下：  
- 十进制：`&#0124;`
- 十六进制：`&#xfe24;`

**需要特别注意的是，十进制必须至少4位数，也就是说如果不足4位，则“&#”后面补0，再强调一遍，千万不要省略0。**

2. CSS字符计算方式  
对于Unicode，CSS采用十六进行方式进行计算，并以`\{unicode}`方式进行表示，同样以`|`字符为例，CSS的表示方式如下：  
````css
/* 这里并没有4个字符的要求，所以\007C并不是必须的 */
span:before {
    content : '\7C'
}
````
3. JS计算方式  
对于Unicode，JS也采用十六进制的方式进行计算，但以“\u{unicode}”的方式表示，继续以“|”字符为例，JS的表示如下：
````js
document.querySelector('span').innerHTML = '\u007C';
````

**这里需要特别指出的是，对于innerHTML的赋值即可以采用`\u007C`方式，也可以采用`&#0124;`HTML方式，但对于input输入框，或者alert打印信息，只能采用`\u007C`方式。**

4. 玩转font-awesome  
通过上面的转换关系，现在我们就可以对font-awesome的字体进行任意的使用了，例如以HTML的方式，如下：
````html
<html>
<header>
    <style>
    /* 十六进制 */
    span::after{
        content: "\7c";
        content: "\007c";
    }
    </style>
</header>
<body>
    <!-- 十进制 -->
    <span>&#0124;</span>
    <!-- 十六进制 -->
    <span>&#e007c;</span>
    <span id="js"></span>
    <script>
    // 十六进制
    document.querySelector('#js').innerHTML = '\u007C';
    </script>
</body>
</html>
````

**这也是我们在开发过程中、html、css、js对于iconfont的表述不一致的原因**

### String.fromCharCode方法
静态 String.fromCharCode() 方法返回由指定的UTF-16代码单元序列创建的字符串。
`String.fromCharCode(num1, ..., numN) `
- 参数: num1, ..., numN
    一系列UTF-16代码单元的数字。 范围介于0到65535（0xFFFF）之间。 大于0xFFFF的数字将被截断。 不进行有效性检查。
- 返回值
    一个长度为N的字符串，由N个指定的UTF-16代码单元组成.

````
String.fromCharCode(65, 66, 67);  // returns "ABC"
String.fromCharCode(0x2014)       // returns "—"
String.fromCharCode(0x12014)      // also returns "—"; the digit 1 is truncated and ignored
````
