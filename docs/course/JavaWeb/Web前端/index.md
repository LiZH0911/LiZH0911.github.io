# Web前端

## 一、HTML-CSS

### 1.1 **HTML-CSS 入门**

**Web 标准**：Web 标准也称网页标准，由一系列的标准组成，大部分由 W3C (World WideWeb Consortium，万维网联盟) 负责制定

**网页的组成**：

- HTML：超文本标记语言，用于创建网页结构
- CSS：层叠样式表，用于创建网页样式
- JavaScript：脚本语言，用于创建网页交互

**HTML（HyperText Markup Language）**：超文本标记语言，用于定义网页内容的含义和结构

- 超文本：超越了文本的限制，比普通文本更强大。除了文字信息，还可以定义图片、音频、视频等内容。
- 标记语言：由标签`<标签名>`构成的语言
- [HTML官方文档](https://developer.mozilla.org/zh-CN/docs/Web/HTML)

**CSS（Cascading Style Sheet）**：层叠样式表，用于控制页面的样式（表现）。

- [CSS官方文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS)

**HTML 基本骨架标签**：

```html
<html>
    <head>
        <title>HTML快速入门</title>
    </head>
    <body>
        <h1>这是一个一级标题</h1>
        <img src="图片地址">
        <p>这是一个段落</p>
    </body>
</html>
```

**前端开发工具**：VS code

### 1.2 **HTML-CSS 常见标签和样式**

**标题标签**：`<h1>一级标题</h1>` ~ `<h6>六级标题</h6>`

**超链接标签**：`<a href="https://www.cctv.com">央视网</a>`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>【新思想引领新征程】推进长江十年禁渔 谱写长江大保护新篇章</title>
</head>
<body>
  <!-- 定义一个标题, 标题内容: 【新思想引领新征程】推进长江十年禁渔 谱写长江大保护新篇章 -->
  <h1>【新思想引领新征程】推进长江十年禁渔 谱写长江大保护新篇章</h1>

  <!-- 定义一个超链接, 里面展示 央视网 -->
  <!-- a 超链接标签:
        href: 链接地址 - url地址
        target: 打开方式 
          _blank: 新窗口打开
          _self: 本窗口打开(默认)
    -->
  <a href="https://www.cctv.com" target="_blank">央视网</a> 
  2024年05月15日 20:07

</body>
</html>
```

**CSS 样式**：

1. 行内样式：body 内定义`<span style="color: gray;">2024年05月15日 20:07</span>`
2. 内部样式：head 内定义`<style>span{color: gray;}</style>`
3. 外部样式：.css 文件内定义，然后 head 内引入该文件 `<link rel="stylesheet" href="css/news.css">`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>【新思想引领新征程】推进长江十年禁渔 谱写长江大保护新篇章</title>
    
  <!-- 方式二: 内部样式 -->
  <style>
    span{
      /* 关键字 */
      /* color: gray; */

      /* RGB表示法 */
      /* color: rgb(255, 120, 0); */

      /* RGBA表示法 */
      /* color: rgba(255, 120, 0, 0); */

      /* 十六进制表示法 */
      /* color: #0000ff; */
      color: #b2b2b2;
    }
  </style>

  <!-- 方式三: 外部样式 -->
  <!-- <link rel="stylesheet" href="css/news.css"> -->
</head>

<body>
  <h1>【新思想引领新征程】推进长江十年禁渔 谱写长江大保护新篇章</h1>
  
  <a href="https://www.cctv.com" target="_blank">央视网</a> 

  <!-- 方式一: 行内样式 -->
  <!-- <span style="color: gray;">2024年05月15日 20:07</span> -->

</body>
</html>
```

**CSS 选择器**：

- 元素选择器
- 类选择器
- ID选择器
- ……

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>【新思想引领新征程】推进长江十年禁渔 谱写长江大保护新篇章</title>
  <style>
    /* 元素选择器 */
    /* span {
      color: #b2b2b2;
    } */

    /* 类选择器 */
    /* .cls {
      color: #ff0000;
    } */

    /* ID选择器 */
    #time {
      color: #b2b2b2;
    }

    a {
      /* 去除超链接下方的下划线 */
      text-decoration: none;
    }
  </style>
</head>

<body>
  <h1>【新思想引领新征程】推进长江十年禁渔 谱写长江大保护新篇章</h1>
  
  <a href="https://www.cctv.com" target="_blank">央视网</a>
  
  <span class="cls" id="time">2024年05月15日 20:07</span>

</body>
</html>
```

**视频标签**：`<video src="视频地址" controls width="80%"></video>`

**图片标签**：` <img src="图片地址" width="80%"></img>`

> 宽度和高度只设置一个即可, 另一个会等比例缩放

**段落标签**：`<p>段落</p>`

**加粗**：

- `<b>加粗文本</b>`
- `<strong>央视网消息</strong>`

**下划线**：

- `<u>下划线文本</u>`
- `<ins>插入的文本</ins>`

**斜体**：

- `<i>斜体文本</i>`
- `<em>强调的文本</em>`

**删除线**：

- `<s>删除的文本</s>`
- `<del>删除的文本</del>`

**字符实体**：

- `&nbsp;`：空格
- `&lt;`：小于符号
- `&gt;`：大于符号

**首行缩进**：

- `&nbsp;&nbsp;&nbsp;&nbsp;`
  - CSS 样式：`p{text-indent: 2em;}`



### 1.3 **HTML-CSS 盒子模型**



**内联标签**：`<span></span>`

- 用途：包裹行内内容
- 不会换行

**div 内联标签**：