---
title:  "HTML"
date:   2020-06-13 20:19:09 +0800
categories: Frontend
---

## head tag

## 段落

```html
<p>段落</p> <!--paragraph段落-->
```

## 强调

```html
<em>斜体</em> <!--emphasis强调-->
<strong>加粗</strong> <!--加粗-->
```

## 标题

```html
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
```

## 块

`<span>span标签是没有语义的，目的是设置单独的样式</span>`

## 短引用

```html
<q>引用内容</q>
```

## 换行

```html
换<br>
行<br />
的内容
```

## 横线

```html
横
<hr>
线
<hr />
的内容
```

## 空格

`空&nbsp;&nbsp;&nbsp;&nbsp;格`

## 长引用

```html
<blockquote>
青青园中葵，朝露待日晞。<br />
阳春布德泽，万物生光辉。<br />
常恐秋节至，焜黄华叶衰。<br />
百川东到海，何时复西归。<br />
少壮不努力，老大徒伤悲。<br />
</blockquote> <!--长引用-->
```

## 联系地址：`<!--各种地址-->`

```html
<address>北京市海淀区</address>
<address>luo1945049855@gmail.com</address>
```

## 单行代码

```html
<p>我们可能知道水平渐变的实现，类似这样：<code>{background-image:linear-gradient(left, red 100px, yellow 200px);}</code></p>
```

## 预处理`<pre>`

常用于处理代码

```js
<pre>
var msg = "hello world";
for (int i = 0; i < 10; ++i) {
    alert(msg);
}
<pre>
```

## 列表

```html
<ul>
    <li>这是我的第一个列表</li>
</ul>

<ol>
    <li>我的第一个列表信息</li>
</ol>
```

## 模块

`<div id="div_id">这是一个模块</div>`

## 表格

```html
<style type="text/css">
table tr td,th{border:1px solid blue;}
</style>
<table summary="员工信息登记">
    <caption>员工信息登记表</caption><!--为表格添加标题-->
    <tbody>
        <tr>
            <th>姓名</th>
            <th>性别</th>
            <th>年龄</th>
        </tr>
        <tr>
            <td>张三</td>
            <td>男</td>
            <td>10</td>
        </tr>
    </tbody>
</table>
```

## 超链接

```html
<a href="http://www.google.com" title="Goolge" target="_blank">google</a>

<a href="mailto:yy@qq.com?cc=cc@qq.com&bcc=bcc@qq.com&subject=测试邮箱&body=这是内容" title="发送邮件给我">点击发送</a>
```

## 图片

```html
<img src="https://www.google.com/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png" alt="当图片下载失败时可见" title="图片标题（tooltip功能)" />
```

## 表单form

```html
<!--method: get/post action: 浏览者输入的数据被传送到的地方-->
<form method="post" action="save.php">
    <label>账户：</label>
    <input type="text" name="username" value="zhangsan" />
    <br />
    <label>密码：</label>
    <input type="password" name="pass" value="1234" />
    <br />
    <input type="submit" value="确定" name="submit" />
    <input type="reset" value="清空" name="reset" />
    <br>
    <label>联系我们</label>
    <textarea cols="50" rows="10"></textarea> <!--文本域-->
    <!--单选框-->
    <br>
    <label>性别</label>
    <br>
    <!--for属性关联label与radio-->
    <label for="male">男</label>
    <input type="radio" name="gender" id="male"/>
    <label for="female">女</label>
    <input type="radio" name="gender" id="female"/>
    <br>
    <!--复选框-->
    <label>兴趣</label>
    <br>
    <label for="id_basketball">篮球：</label>
    <input type="checkbox" name="basketball" id="id_basketball"/>
    <label for="id_football">足球：</label>
    <input type="checkbox" name="football" id="id_football" value="football" checked /> <!--value为提交到服务器的值-->
    <br>
    <label>兴趣：</label>
    <select multiple="multiple"><!--多选-->
        <option value="1">篮球</option>
        <option value="2">足球</option>
        <option value="3" selected="selected">F1</option>
    </select>
</form>
```

## DOCTYPE

document type declaration(DTD)

<!DOCTYPE>是HTML中的第一个标签。用来通知浏览器网页的HTML版本。

### 在HTML4.01中，有三种`<!DOCTYPE>`声明。

1. HTML4.01Strict: This DTD contains all HTML elements and attributes, but does NOT INCLUDE presentational or deprecated elements (like font). Framesets are not allowed.

2. HTML 4.01 Transitional: This DTD contains all HTML elements and attributes, INCLUDING presentational and deprecated elements (like font). Framesets are not allowed.

3. HTML 4.01 Frameset: This DTD is equal to HTML 4.01 Transitional, but allows the use of frameset content.

### 在HTML5中，只有一种声明，即：`<!DOCTYPE html>`