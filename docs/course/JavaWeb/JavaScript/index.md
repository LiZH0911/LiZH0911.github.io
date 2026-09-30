# JavaScript

相关链接：

- [廖雪峰 JavaScript 教程](https://liaoxuefeng.com/books/javascript/introduction/index.html)

## 一、JavaScript 核心语法

### 1.1 **JavaScript 简介**

**JavaScript（简称 JS）**是一门跨平台、面向对象的脚本语言，是用来控制网页行为，实现页面的交互效果。

**JavaScript 组成**：

- **ECMAScript**：规定了 JS 基础语法核心知识，包括变量、数据类型、流程控制、函数、对象等。
- **BOM**：浏览器对象模型，用于操作浏览器本身，如：页面弹窗、地址栏操作、关闭窗口等。
- **DOM**：文档对象模型，用于操作 HTML 文档，如：改变标签内的内容、改变标签内字体样式等。

### 1.2 **JavaScript 引入方式**

**1.2.1 内部脚本**

内部脚本是指将 **JS 代码定义在 HTML 页面中**

- JS 代码位于`<script></script>`标签内
- 一般会把`<script></script>`标签置于`<body>`元素的底部，可改善显示速度

**1.2.2 外部脚本**

外部脚本是指将 **JS 代码定义在外部的 JS 文件中**，然后引入到 HTML 页面中

```html
<body>
  <script>
    //1. 内部脚本
    alert('Hello JS');
  </script>

  <!-- 2. 外部脚本 -->
  <script src="js/demo.js"></script>
</body>
```

### 1.3 **变量和常量**

JS 中用 `let` 关键字来声明变量（弱类型语言，变量可以存放不同类型的值）

变量名需要遵循如下规则:

* 只能用字母、数字、下划线(`_`)、美元符号(`$`)组成，且数字不能开头
* 变量名严格区分大小写，如 `name` 和 `Name` 是不同的变量
* 不能使用关键字,如:`let`、`var`、`if`、`for`等

```html
<body>
  <script>
    //1. 声明变量
    let a = 10;
    a = "Hello";
    a = true;

    // 声明一个变量b=20
    let b = 20;

    alert(a); //弹出框

    //2. 声明常量
    const PI = 3.14;
    //PI = 5.0;

    console.log(PI); //输出到控制台
    // document.write(PI); //输出到body区域(不常用)
  </script>
</body>
```

### 1.4 **数据类型**

JavaScript的数据类型分为:基本数据类型和引用数据类型（对象）。基本数据类型：

* **number**：数字（整数、小数、`NaN`）
* **boolean**：布尔。`true`, `false`
* **null**：对象为空。JavaScript是大小写敏感的,因此 `null`、`Null`、`NULL` 是完全不同的
* **undefined**：当声明的变量未初始化时，该变量的默认值是 `undefined`
* **string**：字符串。单引号、双引号、反引号皆可，推荐使用**单引号**

```html
<body>
  <script>
    //1. 数据类型
    // alert(typeof 10); //number
    // alert(typeof 1.5); //number
    
    // alert(typeof true); //boolean
    // alert(typeof false); //boolean

    // alert(typeof "Hello"); //string
    // alert(typeof 'JS'); //string
    // alert(typeof `JavaScript`); //string 反引号，在键盘Tab上方

    // alert(typeof null); //null ? -> object

    let a ;
    alert(typeof a); //undefined

    //2. 模板字符串 - 简化字符串拼接
    let name = 'Tom';
    let age = 18;

    // 模板字符串${}
    console.log('我是'+name+', 我今年'+age+'岁');
    console.log(`我是${name}, 我今年${age}岁`);
  </script>
</body>
```

### 1.5 **函数**

- 具名函数
- 匿名函数
    - 函数表达式
    - 箭头函数
  
```html
<body>
  <script>
    //1. 函数定义及调用 - 具名函数
    // function add(a,b){
    //   return a + b;
    // }

    // let result = add(10,20);
    // console.log(result);
    

    //2. 函数定义及调用 - 匿名函数
    //2.1 函数表达式
    // let add = function(a,b){
    //   return a + b;
    // }

    // let result = add(100,200);
    // console.log(result);

    //2.2 箭头函数
    let add = (a,b) => {
      return a + b;
    }

    let result = add(1000,2000);
    console.log(result);
  </script>
</body>
```

### 1.6 **自定义对象**

```html
<body>
  <script>
    //1. 自定义对象
    // let user = {
    //   name: 'Tom',
    //   age: 18,
    //   gender: '男',
    //   sing: function(){
    //     alert(this.name + '悠悠的唱着最炫的民族风~')
    //   }
    // }

    let user = {
     name: 'Tom',
     age: 18,
     gender: '男',
     sing(){
       alert(this.name + '悠悠的唱着最炫的民族风~')
     }
    }

    // let user = {
    //   name: 'Tom',
    //   age: 18,
    //   gender: '男',
    //   sing: () => { //注意: 在箭头函数中, this并不指向当前对象 - 指向的是当前对象的父级 【不推荐】
    //     alert(this + ':悠悠的唱着最炫的民族风~')
    //   }
    // }

    //2. 调用对象属性/方法
    alert(user.name);
    user.sing();
  </script>
</body>
```

### 1.7 **JSON**

**JSON**：JavaScript Object Notation，JavaScript 对象标记法（JS对象标记法书写的文本）

由于其语法简单，层次结构鲜明，现多用于作为数据载体，在网络中进行数据传输。

**JS 对象与 JSON 字符串转换**

- `JSON.stringify`：将 JS 对象转换为 JSON 字符串
- `JSON.parse`：将 JSON 字符串转换为 JS 对象

```html
<body>
  <script>
    // JSON - JS 对象标记法
    let person = {
      name: 'itcast',
      age: 18,
      gender: '男'
    }
    alert(JSON.stringify(person)); // js 对象 --> json 字符串

    let personJson = '{"name": "heima", "age": 18}'; # 纯文本的 json 字符串
    alert(JSON.parse(personJson).name);// json 字符串 --> js 对象
  </script>
</body>
```

### 1.8 **DOM**

**1.8.1 DOM 简介**

**DOM**：Document Object Model，文档对象模型。DOM 把 HTML 文档抽象成一棵节点树，JS 通过操作这棵树来动态改变页面内容、结构和样式。

DOM 将 HTML 标记语言的各个组成部分封装为对应的对象：

* Document：整个文档对象
* Element：元素对象
* Attribute：属性对象
* Text：文本对象
* Comment：注释对象

JavaScript 通过 DOM，就能够对HTML进行操作：

* 改变 HTML 元素的内容
* 改变 HTML 元素的样式（CSS）
* 对 HTML DOM 事件作出反应
* 添加和删除 HTML 元素

**1.8.2 DOM 操作**

**如何获取 DOM 对象**：

* `document.querySelector('选择器')`
* `document.querySelectorAll('选择器')`


```html
<body>

  <h1 id="title1">11111</h1>
  <h1>22222</h1>
  <h1>33333</h1>

  <script>
    // 修改第一个h1标签中的文本内容
    // 1. 获取DOM对象（获取单个h1标签）
    // let h1 = document.querySelector('#title1');
    // let h1 = document.querySelector('h1'); // 获取第一个h1标签
    // 2. 调用DOM对象中属性或方法
    // h1.innerHTML = '修改后的文本内容';

    // 1. 获取DOM对象（获取多个h1标签）
    let hs = document.querySelectorAll('h1'); 
    // 2. 调用DOM对象中属性或方法
    hs[0].innerHTML = '修改后的文本内容';
  </script>
</body>
```

### 1.9 **事件监听**

**1.9.1 事件监听语法**

`事件源.addEventListener('事件类型'，事件触发执行的函数);`

**事件监听三要素**：

* **事件源**：触发事件的 DOM 对象，可通过 `document.querySelector('选择器')` 获取 DOM 对象
* **事件类型**：比如：鼠标单击 `click`
* **事件触发执行的函数**


```html
<body>
  
  <input type="button" id="btn1" value="点我一下试试1">
  <input type="button" id="btn2" value="点我一下试试2">

  <script>
    //事件监听 - addEventListener (可以多次绑定同一事件)
    document.querySelector('#btn1').addEventListener('click', () => {
      console.log('试试就试试~~');
    });
    document.querySelector('#btn1').addEventListener('click', () => {
      console.log('试试就试试22~~');
    });

    //事件绑定-早期写法 - onclick (如果多次绑定同一事件, 覆盖) - 了解
    document.querySelector('#btn2').onclick =  () => {
      console.log('试试就试试~~');
    }
    document.querySelector('#btn2').onclick =  () => {
      console.log('试试就试试22~~');
    }
  </script>
</body>
```

**1.9.2 常见事件**

为了优化代码的复用性，对 HTML 中的 JS 代码进行模块化，各个模块如下：

`./js/eventDemo.js`模块：

```javascript
import { printLog } from "./utils.js";

//click: 鼠标点击事件
document.querySelector('#b2').addEventListener('click', () => {
    printLog("我被点击了...");
})

//mouseenter: 鼠标移入
document.querySelector('#last').addEventListener('mouseenter', () => {
    printLog("鼠标移入了...");
})

//mouseleave: 鼠标移出
document.querySelector('#last').addEventListener('mouseleave', () => {
    printLog("鼠标移出了...");
})

//keydown: 某个键盘的键被按下
document.querySelector('#username').addEventListener('keydown', () => {
    printLog("键盘被按下了...");
})

//keyup: 某个键盘的键被抬起
document.querySelector('#username').addEventListener('keyup', () => {
    printLog("键盘被抬起了...");
})

//blur: 失去焦点事件
document.querySelector('#age').addEventListener('blur', () => {
    printLog("失去焦点...");
})

//focus: 元素获得焦点
document.querySelector('#age').addEventListener('focus', () => {
    printLog("获得焦点...");
})

//input: 用户输入时触发
document.querySelector('#age').addEventListener('input', () => {
  printLog("用户输入时触发...");
})

//submit: 提交表单事件
document.querySelector('form').addEventListener('submit', () => {
    alert("表单被提交了...");
})
```

`./js/utils.js`模块：

```javascript
export function printLog(msg){
  console.log(msg);
}
```

在 HTML 文档中引入`./js/eventDemo.js`模块：

```html
<body>
    <script src="./js/eventDemo.js" type="module"></script>
</body>
```