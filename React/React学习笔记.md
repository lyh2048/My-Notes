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

ES6中如何定义类

```html
<script>
    // ES6中如何定义类
    class Person {
        // 构造方法
        constructor(name, age) {
            this.name = name;
            this.age = age;
        }
        // 定义方法
        running() {
            console.log(this.name, "running...");
        }
    }
    const p = new Person("Tom", 18);
    console.log(p.name, p.age);
    p.running();
</script>
```

ES6中类的继承

```html
<script>
    // 面向对象的三大特性：封装、继承、多态
    // 继承：1、减少重复代码 2、多态的前提
    class Person {
        constructor(name, age) {
            this.name = name;
            this.age = age;
        }

        running() {
            console.log(this.name, "running...");
        }
    }

    class Student extends Person {
        constructor(name, age, sno) {
            super(name, age);
            this.sno = sno;
        }
    }

    const stu = new Student("Tom", 20, "110");
    console.log(stu.name, stu.age, stu.sno);
    stu.running();

    class Teacher extends Person {
        constructor(name, age, tno) {
            // 子类中是必须初始化父类对象
            super(name, age);
            this.tno = tno;
        }
    }

    const t = new Teacher("Sam", 44, "000");
    console.log(t.name, t.age, t.tno);
    t.running();
</script>
```

电影列表展示

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>电影列表</title>
</head>
<body>
    <div id="app"></div>
    <!-- 引入依赖 -->
    <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
    <!-- 编写React代码 -->
    <script type="text/babel">
        class App extends React.Component {
            constructor() {
                super();
                this.state = {
                    movies: ["大话西游", "盗梦空间", "战狼", "流浪地球", "星际穿越"]
                }
            }

            render() {
                let result = []
                for (let movie of this.state.movies) {
                    result.push(<li>{movie}</li>);
                }

                return (
                    <div>
                        <h2>电影列表1</h2>
                        <ul>{result}</ul>
                        <h2>电影列表2</h2>
                        <ul>{
                            this.state.movies.map((item) => {
                                return <li>{item}</li>
                            })
                        }</ul>
                    </div>
                )
            }
        }

        ReactDOM.render(<App/>, document.getElementById("app"));
    </script>
</body>
</html>
```

计数器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>计数器</title>
</head>
<body>
    <div id="app"></div>
    <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
    <script type="text/babel">
        class App extends React.Component {
            constructor(props) {
                super(props);
                this.state = {
                    cnt: 0
                }
            }

            render() {
                return (
                    <div>
                        <h2>当前计数：{this.state.cnt}</h2>
                        <button onClick={this.increment.bind(this)}>+1</button>
                        <button onClick={this.decrement.bind(this)}>-1</button>
                    </div>
                )
            }

            increment() {
                console.log("+1");
                this.setState({
                    cnt: this.state.cnt + 1
                })
            }
            
            decrement() {
                console.log("-1");
                this.setState({
                    cnt: this.state.cnt - 1
                })
            }
        }

        ReactDOM.render(<App/>, document.getElementById("app"));
    </script>
</body>
</html>
```

JSX是什么？

JSX是一种JavaScript的语法扩展，它用于描述我们的UI界面，并且其完全可以和JavaScript融合在一起使用。

React认为渲染逻辑本质上与其他UI逻辑存在内在耦合

- 比如UI需要绑定事件
- 比如UI中需要展示数据状态，在某些状态改变时，又需要改变UI

它们之间是密不可分的，所以React没有将标记分离到不同的文件中，而是将它们组合到一起

JSX书写规范：

JSX顶层只能有一个根元素，所以我们很多时候会在外层包裹一个div

为了方便阅读，我们通常在jsx的外层包裹一个小括号()，这样阅读方便，并且JSX可以进行换行书写

JSX中的标签可以是单标签，也可以是双标签

注意：如果是单标签，必须以`/>`结尾

JSX中的注释：`{/* 我是注释 */}`

JSX嵌入数据

1、在{}中可以正常显示的内容

`String` `Number` `Array`

2、在{}中不能显示（忽略）

`null` `undefined` `Boolean`

3、对象类型不能作为子元素

JSX嵌入表达式

1、运算符表达式

2、三元表达式

3、进行函数调用

JSX绑定属性

```html
    <script type="text/babel">
        class App extends React.Component {
            constructor(props) {
                super(props);
                this.state = {
                    title: "标题",
                    imgUrl: "https://placekitten.com/200/300",
                    link: "https://www.baidu.com",
                    active: true
                }
            }

            render() {
                return (
                    <div>
                        <h1>Hello React!</h1>
                        {/* 1、绑定普通属性 */}
                        <h2 title={this.state.title}>我是标题</h2>
                        <img src={this.state.imgUrl} alt="kitten"/>
                        {/* 2、绑定class */}
                        <a href={this.state.link} target="_blank" className="box">百度一下</a> 
                        <div className={"title " + (this.state.active ? "active" : "")}>我是div</div>
                        {/* 3、绑定style */}
                        <div style={{color: "red", fontSize: "24px"}}>我是div，绑定style属性</div>
                    </div>
                )
            }
        }

        ReactDOM.render(<App/>, document.getElementById("app"));
    </script>
```

JSX绑定事件

```html
    <script type="text/babel">
        class App extends React.Component {
            constructor(props) {
                super(props);
                this.state = {
                    msg: "您好",
                    movies: ["大话西游", "盗梦空间", "战狼", "流浪地球"]
                }
                // this.btnClick = this.btnClick.bind(this);
            }

            render() {
                return (
                    <div>
                        <h1>Hello React!</h1>
                        {/* 方案一：bind绑定this（显示绑定） */}
                        {/* 方案二：定义函数时，使用箭头函数 */}
                        {/* 方案三：直接传入箭头函数，在箭头函数中调用要执行的函数 */}
                        <button onClick={this.btnClick.bind(this)}>按钮1</button>
                        <button onClick={() => { this.btnClick() }}>按钮2</button>
                        <ul>
                            {
                                this.state.movies.map((item, index, arr) => {
                                    return <li onClick={e => { this.liClick(item, index, e) }}>{item}</li>
                                })
                            }
                        </ul>
                    </div>
                )
            }

            btnClick() {
                console.log("按钮被点击了...");
                console.log(this.state.msg);
            }

            liClick(item, index, event) {
                console.log("li被点击了...");
                console.log(index, item);
                console.log(event);
            }
        }

        ReactDOM.render(<App/>, document.getElementById("app"));
    </script>
```

条件渲染

在vue中，我们会通过指令来控制：比如`v-if`、`v-show`

在react中，所有的条件判断都和普通JavaScript代码一致

```html
    <script type="text/babel">
        class App extends React.Component {
            constructor(props) {
                super(props);
                this.state = {
                    isLogin: true
                }
            }

            render() {
                // 1、if判断（适用于逻辑代码非常多的情况）
                let welcome = null;
                if (this.state.isLogin) {
                    welcome = <h2>欢迎回来~</h2>
                } else {
                    welcome = <h2>请先登录~</h2>
                }
                return (
                    <div>
                        <h1>Hello React!</h1>
                        {welcome}
                        {/* 2、使用三元运算符 */}
                        <button onClick={e => this.btnClick()}>{this.state.isLogin ? "退出" : "登录"}</button>
                        {/* 3、使用逻辑与 */}
                        { this.state.isLogin && <h2>您好，admin</h2>}
                    </div>
                )
            }

            btnClick() {
                this.setState({
                    isLogin: !this.state.isLogin
                })
            }
        }

        ReactDOM.render(<App/>, document.getElementById("app"));
    </script>
```

 列表渲染

真实开发中，我们会从服务器请求到大量的数据，数据会以列表的形式存储。

```html
    <script type="text/babel">
        class App extends React.Component {
            constructor() {
                super();
                this.state = {
                    movies: ["大话西游", "盗梦空间", "战狼", "流浪地球", "星际穿越"]
                }
            }

            render() {
                let result = []
                for (let movie of this.state.movies) {
                    result.push(<li>{movie}</li>);
                }

                return (
                    <div>
                        <h2>电影列表1</h2>
                        <ul>{result}</ul>
                        <h2>电影列表2</h2>
                        <ul>{
                            this.state.movies.map((item) => {
                                return <li>{item}</li>
                            })
                        }</ul>
                    </div>
                )
            }
        }

        ReactDOM.render(<App/>, document.getElementById("app"));
    </script>
```

JSX的本质：实际上，JSX仅仅只是React.createElement(component, props, ... , children)函数的语法糖，所有的JSX最终都会被转换成React.createElement的函数调用。

JSX --> createElement函数 --> ReactElement（对象树）--> ReactDOM.render函数 --> 真实DOM

JSX --> createElement函数 --> ReactElement（对象树）--> ReactDOM.render 函数 --> 原生的控件

## React脚手架

传统的脚手架指的是建筑学的一种结构：在搭建楼房、建筑物时，临时搭建出来的一个框架

编程中提到的脚手架，其实是一种工具，帮助我们快速生成项目的工程化结构

总结：脚手架让项目从搭建到开发，再到部署，整个流程变得快速和便捷

- Vue的脚手架：vue-cli
- Angular的脚手架：angular-cli
- React的脚手架：create-react-app

它们的作用都是帮助我们生成一个通用的目录结构，并且已经将我们所需的工程环境配置好。

安装脚手架

```bash
npm install -g create-react-app
```

创建React项目

```bash
create-react-app 项目名称
```

注意：项目名称不能包含大写字母

启动项目

```bash
cd 项目目录
yarn start
```

## 组件化开发

分而治之的思想

分而治之是软件工程的重要思想，是复杂系统开发和维护的基石

而前端目前的模块化和组件化都是基于分而治之的思想

什么是组件化开发？

我们需要通过组件化的思想来思考整个应用程序：

- 我们将一个完整的页面分成很多个组件

- 每个组件都用于实现页面的一个功能块

- 而每一个组件又可以进行细分

- 而组件本身又可以在多个地方进行复用

组件化是React的核心思想

组件化思想的应用：

- 有了组件化的思想，我们在以后的开发中就要充分利用它
- 尽可能的将页面拆分成一个个小的、可复用的组件
- 这样让我们的代码更加方便组织和管理，并且扩展性更强

类组件

类组件的定义有如下要求：

- 组件名称是大写字符开头（无论类组件还是函数组件）
- 类组件需要继承自React.Component
- 类组件必须实现render函数

组件的定义方式

```jsx
// 类组件的定义
export default class App extends React.Component {
    constructor() {
        super();
        this.state = {
            msg: "您好，App组件"
        }
    }

    render() {
        return (
            <div>
                <span>我是App组件</span>
                <h2>{this.state.msg}</h2>
            </div>
        )
    }
}
```

```jsx
// 函数组件的定义
export default function App() {
    return (
        <div>我是函数组件</div>
    )
}
// 特点：
// 1、没有this对象
// 2、没有内部的状态（可以使用hooks解决）
```

render函数的返回值：

- React元素
- 数组或fragments
- Portals
- 字符串或数值类型
- 布尔类型或null：什么都不渲染

生命周期

很多事物都有从创建到销毁的整个过程，这个过程称之为`生命周期`

![image-20211122165735596](assets/image-20211122165735596.png)

生命周期是一个抽象概念，在生命周期的整个过程中，分成了很多个阶段：

- 比如装载阶段（Mount）
- 比如更新阶段（Update）
- 比如卸载阶段（Unmount）

![image-20211122165812449](assets/image-20211122165812449.png)

React内部为了告诉我们当前处于哪些阶段，会对我们组件内部实现某些函数进行回调，这些函数就是生命周期函数。

```jsx
import React, { Component } from 'react'


class Component1 extends Component {
    render() {
        return (
            <div>
                我是Component1组件
            </div>
        )
    }

    componentWillUnmount() {
        console.log("调用了Component1的componentWillUnmount方法");
    }
}


export default class App extends Component {
    constructor() {
        super();
        this.state = {
            cnt: 0,
            isShow: true
        }
        console.log("执行了组件的constructor方法");
    }

    render() {
        console.log("执行了组件的render方法");
        return (
            <div>
                我是App组件
                <h2>当前计数：{this.state.cnt}</h2>
                <button onClick={e => this.increment()}>+1</button>
                <hr />
                <button onClick={e => this.changeComponent1Show()}>切换</button>
                {this.state.isShow && <Component1 />}
            </div>
        )
    }

    increment() {
        this.setState({
            cnt: this.state.cnt + 1
        })
    }

    changeComponent1Show() {
        this.setState({
            isShow: !this.state.isShow
        })
    }

    componentDidMount() {
        console.log("执行了组件的componentDidMount方法");
    }

    componentDidUpdate() {
        console.log("执行了组件的componentDidUpdate方法");
    }
}

```

constructor中通常只做两件事情：

1、通过给this.state赋值对象来初始化内部state

2、为事件绑定实例（this）

componentDidMount

- 依赖于DOM的操作可以在这里进行
- 可以在此处发送网络请求（官方建议）
- 可以在此处添加一些订阅（在componentWilUnmount中取消订阅）

componentDidUpdate

- 当组件更新后，可以在此处对DOM进行操作
- 如果你对更新前后的props进行了比较，也可以选择在此处进行网络请求（例如，当props未发生变化时，则不会执行网络请求）

componentWillUnmount

- 在此方法中执行必要的清理操作
- 例如，清除timer，取消网络请求或者清除在componentDidMount中的订阅等

组件嵌套

```jsx
import React, { Component } from 'react'

// Header
function Header() {
    return <h2>我是Header组件</h2>
}

// Main
function Main() {
    return (
        <div>
            <h2>我是Main组件</h2>
            <Banner/>
            <ProductList/>
        </div>
    )
}

// Banner
function Banner() {
    return <h3>我是Banner组件</h3>
}

// 商品列表
function ProductList() {
    return (
        <ul>
            <li>商品1</li>
            <li>商品2</li>
            <li>商品3</li>
            <li>商品4</li>
            <li>商品5</li>
        </ul>
    )
}

// Footer
function Footer() {
    return <h2>我是Footer组件</h2>
}


export default class App extends Component {
    render() {
        return (
            <div>
                <Header/>
                <Main/>
                <Footer/>
            </div>
        )
    }
}

```

组件之间的通信

`父传子-函数组件`

```jsx
import React, { Component } from 'react'


function Child(props) {
    const { name, age } = props;
    return <h2>{name + " " + age}</h2>
}

export default class App extends Component {
    render() {
        return (
            <div>
                <Child name="tom" age="18" />
                <Child name="sam" age="48" />
            </div>
        )
    }
}
```

`父传子-类组件`

```jsx
import React, { Component } from 'react'

class Child extends Component {
    render() {
        const { name, age } = this.props;
        return (
            <div>
                <h2>子组件展示的数据：{name + " " + age}</h2>
            </div>
        )
    }
}


export default class App extends Component {
    render() {
        return (
            <div>
                <Child name="tom" age="18" />
                <Child name="sam" age="48" />
            </div>
        )
    }
}

```

`属性验证`

```jsx
import React, { Component } from 'react'
import PropTypes from 'prop-types'


function Child(props) {
    const { name, age, names } = props;
    return (
        <div>
            <h2>{name + "--" + age}</h2>
            <ul>
                {
                    names.map((item, index) => {
                        return <li key={index}>{index + "-" + item}</li>
                    })
                }
            </ul>
        </div>
    )
}

Child.protoTypes = {
    name: PropTypes.string.isRequired,
    age: PropTypes.number,
    names: PropTypes.array
}


Child.defaultProps = {
    name: "test",
    age: 67,
    names: ["x", "y", "z"]
}


export default class App extends Component {
    render() {
        return (
            <div>
                <Child name="tom" age={18} names={["a", "b", "c"]} />
                <Child name="sam" age={48} names={["b", "c", "a"]} />
                <Child />
            </div>
        )
    }
}
```

在某些情况下，我们也需要子组件向父组件传递消息：

- 在vue中是通过自定义事件来完成的
- 在React中同样是通过props传递消息，只是让父组件给子组件传递一个回调函数，在子组件中调用这个回调函数即可

`子传父通信`

```jsx
import React, { Component } from 'react'


class CntButton extends Component {
    render() {
        const { increment } = this.props;
        return (
            <div>
                <button onClick={increment}>+1</button>
            </div>
        )
    }
}

export default class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            cnt: 0
        }
    }

    render() {
        return (
            <div>
                <h2>当前计数：{this.state.cnt}</h2>
                <button onClick={e => {this.increment()}}>+1</button>
                <CntButton increment={e => {this.increment()}} />
            </div>
        )
    }

    increment() {
        this.setState({
            cnt: this.state.cnt + 1
        })
    }
}

```

`组件通信案例练习`

```jsx
import React, { Component } from 'react'
import TabControl from './TabControl'

export default class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            currentTitle: "新款",
            titles: ['新款', '精选', '流行']
        }
    }
    
    render() {
        const { currentTitle, titles } = this.state;
        return (
            <div>
                <TabControl itemClick={index => this.itemClick(index)} titles={titles} />
                <h2>{currentTitle}</h2>
            </div>
        )
    }

    itemClick(index) {
        console.log(index);
        this.setState({
            currentTitle: this.state.titles[index]
        })
    }

}

```

```jsx
import React, { Component } from 'react'
import PropTypes from 'prop-types'


export default class TabControl extends Component {
    constructor(props) {
        super(props);
        this.state = {
            currentIndex: 0
        }
    }
    render() {
        const { titles } = this.props;
        const { currentIndex } = this.state;
        return (
            <div className="tab-control">
                {
                    titles.map((item, index) => {
                        return <div className={"tab-item " + (index === currentIndex ? "active" : "") } key={index} onClick={e => this.itemClick(index)}><span>{item}</span></div>
                    })
                }
            </div>
        )
    }

    itemClick(index) {
        this.setState({
            currentIndex: index
        })
        const { itemClick } = this.props;
        itemClick(index);
    }
}

TabControl.propTypes = {
    titles: PropTypes.array.isRequired
}

```

`效果展示`

![image-20211122215853733](assets/image-20211122215853733.png)

![image-20211122215907659](assets/image-20211122215907659.png)

`React实现插槽slot`

```jsx
import React, { Component } from 'react'
import NavBar from './NavBar'
import NavBar2 from './NavBar2'

export default class App extends Component {
    render() {
        return (
            <div>
                <NavBar>
                    <span>a</span>
                    <strong>b</strong>
                    <a href="https://www.baidu.com" target="_blank" rel="noreferrer">百度一下</a>
                </NavBar>
                <NavBar2 leftSlot={<span>a</span>}
                         centerSlot={<strong>b</strong>}
                         rightSlot={<a href="https://www.baidu.com" target="_blank" rel="noreferrer">百度一下</a>} />
            </div>
        )
    }
}

```

```jsx
import React, { Component } from 'react'

export default class NavBar extends Component {
    render() {
        return (
            <div className="nav-bar">
                <div className="nav-item nav-left">
                    {this.props.children[0]}
                </div>
                <div className="nav-item nav-center">
                {this.props.children[1]}
                </div>
                <div className="nav-item nav-right">
                {this.props.children[2]}
                </div>
            </div>
        )
    }
}

```

```jsx
import React, { Component } from 'react'

export default class NavBar extends Component {
    render() {
        const { leftSlot, centerSlot, rightSlot } = this.props;
        return (
            <div className="nav-bar">
                <div className="nav-item nav-left">
                    {leftSlot}
                </div>
                <div className="nav-item nav-center">
                    {centerSlot}
                </div>
                <div className="nav-item nav-right">
                    {rightSlot}
                </div>
            </div>
        )
    }
}

```

`跨组件通信`

方式一：通过props属性自上而下（由父到子）进行传递

```jsx
import React, { Component } from 'react'

function ProfileHeader(props) {
    return (
        <div>
            <h2>用户昵称：{props.nickname}</h2>
            <h2>用户等级：{props.level}</h2>
        </div>
    )
}

function Profile(props) {
    return (
        <div>
            <ProfileHeader nickname={props.nickname} level={props.level} />
            <ul>
                <li>设置1</li>
                <li>设置2</li>
                <li>设置3</li>
                <li>设置4</li>
            </ul>
        </div>
    )
}

export default class App extends Component {
    constructor(props) {
        super(props);

        this.state = {
            nickname: "tom",
            level: 100
        }
    }

    render() {
        const { nickname, level } = this.state;
        return (
            <div>
                <Profile nickname={nickname} level={level} />
            </div>
        )
    }
}

```

方式二：使用Context

```jsx
import React, { Component } from 'react'

// 创建Context对象
const UserContext = React.createContext({
    nickname: "test",
    level: 0
})

function ProfileHeader() {
    return (
        <UserContext.Consumer>
            {
                value => {
                    return (
                        <div>
                            <h2>用户昵称：{value.nickname}</h2>
                            <h2>用户等级：{value.level}</h2>
                        </div>
                    )
                }
            }
        </UserContext.Consumer>
    )
}

function Profile() {
    return (
        <div>
            <ProfileHeader />
            <ul>
                <li>设置1</li>
                <li>设置2</li>
                <li>设置3</li>
                <li>设置4</li>
            </ul>
        </div>
    )
}

export default class App extends Component {
    constructor(props) {
        super(props);

        this.state = {
            nickname: "tom",
            level: 100
        }
    }

    render() {
        return (
            <div>
                <UserContext.Provider value={this.state}>
                    <Profile />
                </UserContext.Provider>
            </div>
        )
    }
}

```



## AntDesign库的使用

## Axios库的使用和封装

## React Hooks

## 项目的打包和部署

