# 函数
函数是编程语言中一个重要部分。在ES6之前，JS的函数与最初发明时没有大的变动。这积累了很多问题，某些微妙的行为很容易导致错误，一些基本的操作需要更多的代码。
ES6的函数带来了很大的改变，考虑到了JS开发者多年的抱怨和建议。结果就是大量的对于ES5增量式的特性，减少了出错的可能，功能也更加强大。
## 默认参数
JS中的函数的一个特殊之处是，允许传递任意数量的参数，无论函数声明中有几个参数。开发者能够在函数中处理参数的不同情况，比如说很常用的：当没有传递某个参数时，给它提供一个默认值。本文内容覆盖到ES6之前和ES6中的默认参数，arguments对象的一些重要信息，使用表达式做参数，TDZ。
### 在ES5中模仿默认参数
在ES5和之前，你可能需要这样使用默认参数。
```js
function makeRequest(url, timeout, callback) {

    timeout = timeout || 2000;
    callback = callback || function() {};

    // the rest of the function

}
```
这种方式有一个缺陷，如果timeout的真实值是0，它会被修改为2000，因为0代表着假值。更安全的方法是，使用`typeof`操作符：
```js
function makeRequest(url, timeout, callback) {

    timeout = (typeof timeout !== "undefined") ? timeout : 2000;
    callback = (typeof callback !== "undefined") ? callback : function() {};

    // the rest of the function

}
```
这种方式更安全，但它需要写更多的代码。一些流行的JS库充斥着大量类似的方式。
### ES6中的默认参数
ES6提供了更方便的形式：
```js
function makeRequest(url, timeout = 2000, callback = function() {}) {

    // the rest of the function

}
```

```js
// uses default timeout and callback
makeRequest("/foo");

// uses default callback
makeRequest("/foo", 500);

// doesn't use defaults
makeRequest("/foo", 500, function(body) {
    doSomething(body);
});
```

```js
// uses default timeout
makeRequest("/foo", undefined, function(body) {
    doSomething(body);
});

// uses default timeout
makeRequest("/foo");

// doesn't use default timeout
makeRequest("/foo", null, function(body) {
    doSomething(body);
});
```

### 默认参数对arguments对象的影响
给定默认参数时，arguments对象的行为有些不同。在ES5的非严格模式，arguments会随着命名参数改变。
```js
function mixArgs(first, second) {
    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
    first = "c";
    second = "d";
    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
}

mixArgs("a", "b");
```
输出：
```
true
true
true
true
```
然而在严格模式下，arguments并不会有这个令人混淆的机制。输出：
```
true
true
false
false
```
### 默认参数表达式
一个很有趣的特性是默认参数不必是原始类型。比如，可以执行一个函数，将它的结果作为默认参数：
```js
function getValue() {
    return 5;
}

function add(first, second = getValue()) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 6
```

```js
let value = 5;

function getValue() {
    return value++;
}

function add(first, second = getValue()) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 6
console.log(add(1));        // 7
只有没有提供第二个参数时，getValue才会被调用。
这引出了另一个有趣的能力。可以使用前一个参数，作为后面的参数的默认参数。
```js
function add(first, second = first) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 2
```
这种能力只能对前面的参数起作用，也就是说，前面的参数不能够获取到后面的。

```js
function add(first = second, second) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // throws error
```

### 默认参数TDZ

```js
function add(first = second, second) {
    return first + second;
}

console.log(add(1, 1));         // 2
console.log(add(undefined, 1)); // throws error
```

### 剩余参数
`...`：
+ 是真正的数组
+ 不影响函数的length属性
```js
function pick(object, ...keys) {
    let result = Object.create(null);

    for (let i = 0, len = keys.length; i < len; i++) {
        result[keys[i]] = object[keys[i]];
    }

    return result;
}
```
### 剩余参数的限制
+ 只能有一个
+ 只能在最后

### 剩余参数对arguments对象的影响
剩余参数是被设计用来替换arguments的。事实上，在ES4就抛弃了arguments，增加了剩余参数，然而ES4并没有正式存在过，这个想法却保留下来，并且在ES6重新被引入。（尽管arguments没有被移除）
## 增强函数构造器
支持默认参数和剩余参数
## 展开操作符？？？
```js
let values = [-25, -50, -75, -100]

console.log(Math.max(...values, 0));        // 0
```

## ES6的name属性
ES6给所有的函数增加了name属性。
### name属性的特殊情况

```js
var doSomething = function doSomethingElse() {
    // ...
};
```
doSomethingElse
bind() => "bound"
Function() => "anonymous"
### 理清有歧义的函数
`[[Call]]` `[[Construct]]`
并不是所有的函数都有`[[Construct]]`，所以并不是所有的函数可以用new来调用。箭头函数。
### 在ES5中决定如何调用函数
### new.target元属性
用于区分函数调用。
```js
function Person(name) {
    if (typeof new.target !== "undefined") {
        this.name = name;   // using new
    } else {
        throw new Error("You must use new with Person.")
    }
}

var person = new Person("Nicholas");
var notAPerson = Person.call(person, "Michael");    // error!
```

## 块级作用域的函数
### 何时使用块级作用域的函数

## 箭头函数
ES6一个很重要的部分是箭头函数。 =>,与传统的js函数不同：
* **没有this,super, arguments,new.target
* 不能使用new
* 没有prototype
* 不能改变this
### 语法
### IIFE
### 不做this绑定
### 尾调用的优化
