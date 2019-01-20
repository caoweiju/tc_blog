---
title: 使用post表单提交来跳转页面避免url的参数过长被阻断
date: 2019-01-20 12:14:57
tags:
- post
categories: 
- JavaScript
---
# 414状态码
在前端中，`411	Length Required	`服务器无法处理客户端发送的不带Content-Length的请求信息，有的时候面对这种情况会比较心碎，基本思路如下：
1. 压缩参数
2. 使用post

不过以上并不能很好的处理一些情况，压缩参数这个需要后端配合，使用`post`的话，如果是接口API的话，还好，如果是页面的话，就需要`post`跳转页面。
## post页面跳转
基本思路是使用表单来提交参数，步骤：
1. 创建表单
2. 跳转`action`来指向跳转页面
3. 添加表单数据
4. 插入`dom`中，设置不可见
5. 通过`submit`来提交

基本原生`javascript`的使用：
````
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <button onclick="post()">post</button>
    <script>
        function post() {
            //创建form表单
            var temp_form = document.createElement("form");
            temp_form.action = "https://cn.bing.com";
            //如需打开新窗口，form的target属性要设置为'_blank'
            temp_form.target = "_self";
            temp_form.method = "post";
            temp_form.style.display = "none";
            var PARAMTERS = [{
                name: 'no',
                value: 1
            }, {
                name: 'name',
                value: 'baidu'
            }, {
                name: 'page',
                value: 1
            }, {
                name: 'id',
                value: 'cwj'
            }, {
                name: 'no',
                value: 20
            }, {
                name: 'file',
                value: 'filename'
            }, {
                name: 'name',
                value: 'qq'
            }, {
                name: 'name',
                value: 'wechat'
            }];
            //添加参数
            for (var item in PARAMTERS) {
                var opt = document.createElement("textarea");
                opt.name = PARAMTERS[item].name;
                opt.value = PARAMTERS[item].value;
                temp_form.appendChild(opt);
            }
            document.body.appendChild(temp_form);
            //提交数据
            temp_form.submit();
        }
    </script>
</body>

</html>
````
需要注意：
1. 遇到同名的参数的时候，需要注意参数值的处理，可以看看结果如下图：
![图片描述][./../../imgs/posturl.png]


  [1]: /img/bVZasX