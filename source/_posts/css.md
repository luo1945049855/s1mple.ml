---
title:  "CSS"
date:   2020-06-13 20:19:09 +0800
categories: Frontend
---

## css三种写法，优先级（内联式>嵌入式>外部式）

1. 内联式：写在html标签中。
   
    `<p><span style="color:red">内联式</span></p>`

2. 嵌入式：写在当前文件中。

    ```css
    <style type="text/css">
        span{
            color:blue;
        }
    </style>
    <span>嵌入式</span>
    ```

3. 外部式：写在外部文件中。
   
    `<link href="base.css" rel="stylesheet" type="text/css" />`

### 注意：
    1. 嵌入式写在外部式之后
    2. 相同权值

## 标签选择器

其实就是html代码中的标签。如html、body、h1、p、img。

## 类选择器

.类选器名称{css样式代码;}

```css
<style type="text/css">
    .setGreen{
        color:green;
    }
</style>
<span class="setGreen">类选择器</span>
```

## ID选择器

```css
<style type="text/css">
    #setGreen{
        color:green;
    }
</style>
<span id="setGreen">ID选择器</span>
```

## 类选择器和ID选择器区别

* 类选择器可以多次使用，ID选择器不可。
* 类选择器可以多个同时使用，ID选择器不可。
  
## 子选择器

```css
<style type="text/css">
 .food>li{color:white; border:1px solid red;}
</style>
<ul class="food">
    <li>水果
        <ul>
            <li>香蕉</li>
            <li>苹果</li>
        </ul>
    </li>
    <li>蔬菜
        <ul>
            <li>香蕉</li>
            <li>苹果</li>
        </ul>
    </li>
</ul>
```

## 包含（后代）选择器

```css
<style type="text/css">
 .first li{color:white; border:1px solid red;}
</style>
<ul class="first">
    <li>水果
        <ul>
            <li>香蕉</li>
            <li>苹果</li>
        </ul>
    </li>
    <li>蔬菜
        <ul>
            <li>香蕉</li>
            <li>苹果</li>
        </ul>
    </li>
</ul>
```

## 通用选择器

`*{color:red;}`

## 伪类选择符

给html不存在的标签设置样式，如

```css
<style type="text/css">
    a:hover{
        color:red;
        font-size:20px;
        }
</style>
<a href="http://www.google.com">Google</a>
```

## 分组选择符

为浏览器中多个标签设置同一样式

```css
h1,span{color:red;}
<style type="text/css">
h1,span{color:red;}
.first,#second span{color:green;}
</style>
<p class="first">三年级时，我还是一个<span>胆小如鼠</span>的小女孩，上课从来不敢回答老师提出的问题，生怕回答错了老师会批评我。就一直没有这个勇气来回答老师提出的问题。学校举办的活动我也没勇气参加。</p>
<p id="second">到了三年级下学期时，我们班上了一节公开课，老师提出了一个很<span>简单</span>的问题，班里很多同学都举手了，甚至成绩比我差很多的，也举手了，还说着："我来，我来。"我环顾了四周，就我没有举手。</p>
```

## 继承

CSS的某些样式具有继承性，它允许样式不仅应用于某个特定的html标签元素，而且应用于其后代。

`p{color:red;}`具有继承性；
`p{border:1px solid red;}`不具有继承性。
