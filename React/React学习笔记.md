# React学习笔记

## 概述

React是用于构建用户界面的`JavaScript`库。

现在前端领域最为流行的三大框架：

- Vue
- React
- Angular

其中，Vue和React是国内最为流行的两个框架。

React的特点：

1、声明式编程：它允许我们只需要维护自己的状态，当状态改变时，React可以根据最新的状态去渲染我们的UI界面。

2、组件化开发

![image-20211120204214915](assets/image-20211120204214915.png)

3、多平台适配

 ## Hello React

React开发依赖

开发React必须依赖三个库：

- react：包含react所必须的核心代码
- react-dom：react渲染在不同平台所需要的核心代码
- babel：将jsx转换成React代码的工具

如何添加这三个依赖？

方式一：直接CDN引入

方式二：下载后，添加本地依赖

方式三：通过npm

```html
<script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello React!</title>
</head>
<body>
    <div id="app"></div>
    <!-- 添加React依赖 -->
    <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
    <!-- 注意事项：使用jsx，并且希望script中的jsx代码被解析，必须在script标签中添加一个属性 -->
    <!-- jsx特点：多个标签最外层（根）只能有一个标签 -->
    <script type="text/babel">
        let msg = "Hello World!";

        function btnClick() {
            msg = "Hello React!";
            render();
            console.log("按钮发生了点击");
        }

        function render() {
            ReactDOM.render(
            <div>
                <h2>{msg}</h2>
                <button onClick={btnClick}>改变文本</button>
            </div>, 
            document.getElementById("app"));
        }

        render();
    </script>
</body>
</html>
```

组件化

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello React!</title>
</head>
<body>
    <div id="app"></div>
    <!-- 添加React依赖 -->
    <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
    <script type="text/babel">
        // 封装组件
        class App extends React.Component {
            constructor() {
                super();
                // this.msg = "Hello World!";
                this.state = {
                    msg: "Hello World!"
                }
            }

            render() {
                return (
                    <div>
                        <h2>{this.state.msg}</h2>
                        <button onClick={this.btnClick.bind(this)}>改变文本</button>
                    </div>
                )
            }

            btnClick() {
                // this.msg = "Hello React!";
                this.setState({
                    msg: "Hello React!"
                })
                console.log("按钮发生了点击");
            }
        }
        // 渲染组件
        ReactDOM.render(<App/>, document.getElementById("app"));
    </script>
</body>
</html>
```

## JSX核心语法

## React脚手架

## 组件化开发

## AntDesign库的使用

## Axios库的使用和封装

## React Hooks

## 项目的打包和部署

