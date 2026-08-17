# JavaScript

该笔记参考的课程链接：

- [黑马程序员 AI+JavaWeb](https://www.bilibili.com/video/BV1yGydYEE3H?spm_id_from=333.788.videopod.episodes&vd_source=46f99c7c1ed609a31f70615a4551767f&p=2)
- [廖雪峰 JavaScript 教程](https://liaoxuefeng.com/books/javascript/introduction/index.html)

## 一、JavaScript 核心语法

### 1.1 **简介**

**JavaScript**：JavaScript（简称 JS）是一门跨平台、面向对象的脚本语言，是用来控制网页行为，实现页面的交互效果。JavaScript 和 Java 是完全不同的语言，不论是概念还是设计。但是基础语法类似。

**JavaScript 组成**：

- ECMAScript：规定了 JS 基础语法核心知识，包括变量、数据类型、流程控制、函数、对象等。
- BOM：浏览器对象模型，用于操作浏览器本身，如:页面弹窗、地址栏操作、关闭窗口等。
- DOM：文档对象模型，用于操作 HTML 文档，如:改变标签内的内容、改变标签内字体样式等。

### 1.2 **JavaScript 引入方式**

**内部脚本**：将 JS 代码定义在 HTML 页面中

- JS 代码必须位于`<script></script>`标签之间
- 在HTML文档中，可以在任意地方，放置任意数量的`<script>`标签
- 一般会把脚本置于`<body>`元素的底部，可改善显示速度

**外部脚本**：将 JS 代码定义在外部 JS 文件中，然后引入到 HTML 页面中

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
