# 用模块封装代码
JS将变量暴露到全局作用域的做法，导致它易错，让人困惑。其它语言都有类似包（package）的概念，但在ES6之前，JS文件中定义的任何东西都是在全局共享的。随着web应用越来越复杂，复用也越来越多，这种方式导致命名冲突和安全隐患。ES6的目标之一就是解决作用域的问题，使JS代码更有序。

## 什么是模块(Modules)？

*Moudles*是使用不同的模式加载的JS文件（相对于*scripts*， 原始的加载方式）。这种模式的引入是非常必要的，它的语义与scripts有很大的不同：
1. `Module`模式下自动处于严格模式，而且不可更改。
2. 顶层的变量不会自动暴露到全局作用域，只存在于模块内部。
3. 模块顶层中`this`为`undefined`。
4. 不允许出现html风格的注释（一个过时的特性，为了兼容早期浏览器）。
5. 模块必须导出（export）外部可用的变量。
6. 可以从其他模块导入（import）。

这些改变初看起来不经意，但是这给JS代码加载带来了巨大的改进，这也是我们这一章要讨论的内容。模块的真实威力是能够只导出（export）和导入（import）你需要的东西，而不是所有。深入理解export和import是理解模块与scripts不同的基础。

## 基本的导出

可以使用`export`暴露内容给其它模块。最简单的情况下，可以在任意变量，函数，class前加上`export`，如：

```js
// export data
export var color = "red";
export let name = "Nicholas";
export const magicNumber = 7;

// export function
export function sum(num1, num2) {
    return num1 + num1;
}

// export class
export class Rectangle {
    constructor(length, width) {
        this.length = length;
        this.width = width;
    }
}

// this function is private to the module
function subtract(num1, num2) {
    return num1 - num2;
}

// define a function...
function multiply(num1, num2) {
    return num1 * num2;
}

// ...and then export it later
export { multiply };
```
注意：1，除了`export`关键字，声明的其它部分不变，导出的每个函数和class都会有一个name。这种方式不能导出匿名函数，除非使用`default`关键字。
2，注意`multiply()`函数，在定义的位置并没有导出。这样也是可以的，不一定要导出声明，也可以导出引用。
3.注意函数`substract()`并没有`export`，它在模块外部是不可访问的。

## 基本导入
一旦使用`export`导出一个模块，就可以使用`import`读取它。基本形式：
```js
import { identifier1, identifier2 } from "./example.js";
```

用一个字符串指示模块的路径（称为*模块标识符*）。浏览器中必须要和传入到`<script>`标签的路径相同，也就是说必须带上扩展名。Node端则不需要，依照文件系统惯例，扩展名可以忽略。举例：`example`作为一个包会作为`./example.js`的本地文件。





