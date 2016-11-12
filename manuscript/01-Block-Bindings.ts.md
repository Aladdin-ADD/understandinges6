# 块级变量
长久以来，变量声明是JavaScript中的让人困惑的部分之一。在大多数的C系列的语言中，变量是在声明的地方创建的。但是在js中，变量创建的时机与声明方式有关。ES6提供了新的方法，可以帮助简化对变量作用域的理解。本章阐述了经典的var声明方式让人困惑的根源，引入了新的ES6中新的块级变量，进而给出了使用它们的最佳实践。
## var声明和提升
使用var声明，可以理解为声明在函数（或者全局）作用域的最上面，无论它们实际出现的位置--这称为提升（hoisting），举例：
```js
function getValue(condition) {

    if (condition) {
        var value = "blue";

        // other code

        return value;
    } else {

        // value exists here with a value of undefined

        return null;
    }

    // value exists here with a value of undefined
}
```
如果对js不是很熟悉的话，可能你会认为变量`value`仅是在参数`condition`为真时创建。但实际上无论`condition`是否为真，`value`都会创建。js引擎会把代码修改成下面这样：
```js
function getValue(condition) {

    var value;

    if (condition) {
        value = "blue";

        // other code

        return value;
    } else {

        return null;
    }
}
```
变量value被`提升`到函数开头，而初始化还是在原来的位置。意味着`value`在`else`语句中是可访问的，它的值为未初始化的undefined。

通常开发者都要花一些时间来熟悉js的`提升`，不理解这种独特的行为可能会引发bug。因此，ES6引入了块级声明的方式，它能够更有效的控制变量的生命周期。

## 块级声明
块级声明指在块的外部不能访问的变量。当然，块级作用域也是词法作用域，以下情况会被创建：
1.函数内部；
2.块的内部（`{`和`}`之间）。
块级作用域也是大多数C系列语言的机制，ES6引入它的目的是提高js的灵活性和一致性。

### let声明
let声明的语法与var类似，（声明变量时）只需要把var替换为let，该变量的作用域就限定为当前块（有一些微妙的差异，稍后解释）。由于let声明不会进行提升，因此如果你需要在整个块内访问的话，需要将let声明置于块的开始,举例：
```js
function getValue(condition) {

    if (condition) {
        let value = "blue";

        // other code

        return value;
    } else {

        // value doesn't exist here

        return null;
    }

    // value doesn't exist here
}
```
这个版本的`getValue`函数更接近其它语言的行为。因为我们使用了`let`,而不是`var`，不会发生声明提升。因此在if块的外部，变量`value`不可访问，如果`condition`为假，它根本不会被声明和初始化。
### 禁止重复声明
同一个块内已经声明的变量，如果使用let再次声明，会抛出错误，举例：
```js
var count = 30;

// Syntax error
let count = 40;
```
这个例子中，`count`被声明了2次，第一次使用`var`,第二次使用`let`。因为`let`不会重新定义同一个块已存在的变量，这会导致语法错误。但是，如果在块内部的另一个块内声明同一标示符的变量，这是合法的（太绕口了，看例子）。
```js
var count = 30;

// Does not throw an error
if (condition) {

    let count = 40;

    // more code
}
```
这段代码不会抛出错误，因为它会在if块内创建一个新的变量，名称为`count`。在if内部，新创建的变量`count`会遮蔽（shadow）全局的`count`，而执行出了if，全局的`count`变的又可以访问了。
### 常量声明
ES6中可以使用`const`关键字声明常量，这意味它的值一旦赋值，不可以改变。因此，每一个`const`在声明时，就应该赋给初始值。
```js
// Valid constant
const maxItems = 30;

// Syntax error: missing initialization
const name;
```
常量`maxItems`在声明时有了初始值，不会报错。而`name`没有初始值，会产生一个语法错误。

#### const声明 vs. let声明
与let声明类似，const声明也是块级的。也就是说，（1）出了声明所在的块，这些变量是不可访问；（2）不会进行提升。
```js
if (condition) {
    const maxItems = 5;

    // more code
}

// maxItems isn't accessible here
```
`maxItems`变量是在if内声明的，因此一旦if语句执行完，不再可以访问。
与`let`类似，在同一块内，对已存在的变量再次使用`const`声明会导致语法错误。即使之前的声明不是使用`const`（`var`，`let`），也是一样。
```js
var message = "Hello!";
let age = 25;

// Each of these would throw an error.
const message = "Goodbye!";
const age = 30;
```
单独使用const声明`message`和`age`没有问题，但是上面已经声明过的话，就会报错。
除了上面的共同点，`const`和`let`的一个最大的差别就是：对const变量重新赋值会导致错误（无论是否处于严格模式）。
```js
const maxItems = 5;

maxItems = 6;      // throws error
```
与其它语言类似，`maxItems`不能够赋给一个新值。然而不同点在于，如果是使用`const`声明的对象，是可以修改的。
#### 用const声明对象
`const`声明的变量，阻止了对它的值（指向）本身的修改。对于`const`声明的对象，可以这样理解：它是一个指针，它的指向不可以修改，但是指向的对象的值可以修改。`const`声明的变量好比是你家的门牌号，它本身不会变，但是里面住的人可以换。
```js
const person = {
    name: "Nicholas"
};

// works
person.name = "Greg";

// throws an error
person = {
    name: "Greg"
};
```
这里的`person`是用`const`声明的对象常量，修改`person.name`并不会报错，因为它只是修改了`person`所指向对象的值，而没有修改它的指向。当尝试修改指向时，就会报错。`const`声明对象的这种微妙的差别很容易造成误解。
### TDZ
`let`和`const`声明的变量在声明之前不可访问。如果访问，会抛出一个引用错误（ReferenceError），即使只是进行通常认为的`typeof`这样的“安全操作”。
```js
if (condition) {
    console.log(typeof value);  // ReferenceError!
    let value = "blue";
}
```
例子中使用`let`声明了变量`value`，但这句代码并不会被真正执行，因为上一行会报错。js开发者将这个问题称为TDZ（*temporal dead zone*）。虽然在ES规范中这一概念从未被明确定义，但人们经常用它说明`let`和`const`变量在声明前不可访问的问题。这一部分涉及到了TDZ产生的微妙之处，请注意，虽然例子中使用的是`let`，但是对`const`来说是一样的。
当js引擎在当前块中发现一个变量声明时，要么进行提升（hoisting）,要么将其放入TDZ。任何尝试访问TDZ内变量的行为（比如typeof），都会导致运行时错误。只有变量从TDZ中移除，才能够安全的使用。

在`let`和`const`定义变量之前，使用该变量都会出现这个错误。比如上面例子中使用`typeof`。但你可以在变量所在的块外部使用`typeof`，这样不会发生错误。
```js
console.log(typeof value);     // "undefined"

if (condition) {
    let value = "blue";
}
```
***
TDZ只是块级变量一个特别之处，还有一个是循环中的块级变量。
## 循环中的块绑定
可能开发者最希望使用块绑定的一个场景就是for循环，循环变量（通常为i,j...）只能够在循环体内使用。举例来说，下面的代码在js中并不少见：

```js
for (var i = 0; i < 10; i++) {
    process(items[i]);
}

// i is still accessible here
console.log(i);                     // 10
```
在其它语言中，默认为块级作用域，上面例子中的变量i应该只能够在for循环内部使用。然而，在js中，因为声明提升，在for循环外仍然可以访问到i。在使用let声明时，与我们本来的预期相同：变量i只存在于for循环，在它之外，就不能够访问了:

```js
for (let i = 0; i < 10; i++) {
    process(items[i]);
}

// i is not accessible here - throws an error
console.log(i);
```

### 循环中的函数
由于循环内的var声明的变量能够在外部访问，因此循环中的函数访问这个变量时很容易造成误解：
```js
var funcs = [];

for (var i = 0; i < 10; i++) {
    funcs.push(function() { console.log(i); });
}

funcs.forEach(function(func) {
    func();     // outputs the number "10" ten times
});
```
你可能很天真的认为这个函数应该打印出0~9，但实际输出10*10。这是由于i是由循环的每次迭代都可以访问，这意味着循环内部保存的是同一个变量i的引用。因此当循环执行完，i的值变成10，每次执行console.log(i)，就会输出一个10。
要解决这个问题，开发者通常使用一个立即执行函数（IIFE）在循环内部创建一个变量的拷贝。
```js
var funcs = [];

for (var i = 0; i < 10; i++) {
    funcs.push((function(value) {
        return function() {
            console.log(value);
        }
    }(i)));
}

funcs.forEach(function(func) {
    func();     // outputs 0, then 1, then 2, up to 9
});
```

### 循环中的的let声明
使用let声明可以简化上面使用IIFE的这种用法。在循环的每次执行中，都会创建一个变量并赋值成上一次的那个值。也就是说，你不用使用IIFE就能达到你所期望的结果：

```js
var funcs = [];

for (let i = 0; i < 10; i++) {
    funcs.push(function() {
        console.log(i);
    });
}

funcs.forEach(function(func) {
    func();     // outputs 0, then 1, then 2, up to 9
})
```

### 循环中的的const声明

### 全局块绑定
let与const不同于var的另一点是，在全局作用域的时候。全局作用域中，使用var声明一个变量，会创建一个全局变量，它是全局对象（在浏览器中为window）的属性。这意味着你可能会无意中覆盖现有的全局变量。
```js
// in a browser
var RegExp = "Hello!";
console.log(window.RegExp);     // "Hello!"

var ncz = "Hi!";
console.log(window.ncz);        // "Hi!"
```

```js
// in a browser
let RegExp = "Hello!";
console.log(RegExp);                    // "Hello!"
console.log(window.RegExp === RegExp);  // false

const ncz = "Hi!";
console.log(ncz);                       // "Hi!"
console.log("ncz" in window);           // false
```
当你使用let声明全局变量时，它并不会出现在window对象上。
## 块绑定的最佳实践
应该默认是使用let，而不是var来声明变量。对许多开发者而言，let的行为方式更符合直觉。
在需要数据保护，防止被修改的情况下，应该使用const来声明变量。

然而，有一种观点受到更多开发者的认可：应该默认使用const声明，只有当你知道它的值需要改变时使用let。理由是大多数变量一旦初始化之后都不需要改变，一旦改变它们的值会引发bug。在使用ES6时，建议采用这条规则。

## 总结