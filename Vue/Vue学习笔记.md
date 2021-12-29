# Vue学习笔记

[Vue2.0](https://cn.vuejs.org/)

[Vue3.0](https://v3.cn.vuejs.org/)

## 简介

1、Vue是什么？

Vue是一套用于构建用户界面的渐进式JavaScript框架。

渐进式：Vue可以自底向上逐层应用

简单应用：只需引入一个轻量小巧的核心库

复杂应用：可以引入各式各样的Vue插件

> 后起之秀，生态完善，已然成为国内前端工程师的必备技能。

2、谁开发的？

[Evan You](https://evanyou.me/)

[yyx990803 (Evan You) · GitHub](https://github.com/yyx990803)

![image-20211127202245192](assets/image-20211127202245192.png)

3、Vue的特点

- 采用组件化模式，提高代码复用率，且让代码更好维护。
- 声明式编码，让编码人员无需直接操作DOM，提高开发效率。

## Hello小案例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>初识Vue</title>
    <!-- 引入Vue -->
    <script type="text/javascript" src="../js/vue.js"></script>
</head>
<body>
    <!-- 准备好一个容器 -->
    <div id="root">
        <h1>Hello, {{name}}</h1>
    </div>
    <!-- 引入Vue -->
    <script type="text/javascript">
        // 创建Vue实例
        new Vue({
            el: '#root',
            data: {
                name: '张三'
            }
        })
    </script>
</body>
</html>
```

## 模板语法

