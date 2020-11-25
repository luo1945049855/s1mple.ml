---
title:  "ES6"
date:   2020-06-13 20:19:09 +0800
categories: Frontend
---

## `let`命令

`let`类似与`var`, 但是let所生明的变量，只在let所在的作用域内有效。

1. 不存在变量提升

let要先声明，后使用。否则报错。
var先使用再声明，值为undefined。

2. 暂时性死区

暂时性死区意味着`typeof`不再是百分之百安全操作。
ES6明确规定，如果区块内存在let或const命令，这些命令声明的变量，从一开始就形成了封闭作用域，凡是在声明前使用，就会报错。

```js
var tmp = 123;
if (true) {
    tmp = 'abc'; // ReferenceError.
    typeof tmp; // ReferenceError.
    typeof undefined_variable; // undefined.
    let tmp;
    console.log(tmp); // undefined.
    tmp = 123;
    console.log(tmp); // 123.
}

var x = x; // ok.
let x = x; // error.
```

3. let不允许重复声明

```js
function func(arg) {
    let arg; // arg is duplicate.
}
func() // error.
```

## `const`命令

const声明一个只读的常量，一旦声明，变量的值就不能改变。

const作用域与let命令相同：只在声明所在的块级作用域内有效。

const命令声明的变量也是不提升、存在暂时性死区，只能在声明的位置后面使用。

const命令与let一样不能重复。

```js
const PI = 3.1415;
PI = 3; // error

if (true) {
    const MAX = 5;
}
MAX // Uncaught ReferenceError: MAX is not defined.

if (true) {
    console.log(MAX); // ReferenceError
    const MAX = 5;
}

```
