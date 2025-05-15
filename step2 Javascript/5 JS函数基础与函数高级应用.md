# **JavaScript 函数基础与高级应用**

JavaScript 函数是前端开发的核心概念之一，既可以封装可重用的代码块，也能处理异步编程、回调、闭包等高级应用。

---

## **1. JavaScript 函数基础**
### **1.1 函数的定义**
#### **（1）函数声明（Function Declaration）**
```js
function greet(name) {
    return "Hello, " + name + "!";
}
console.log(greet("Alice")); // Hello, Alice!
```
> **特点**：可以被 **提前调用**（函数提升 Hoisting）

#### **（2）函数表达式（Function Expression）**
```js
const greet = function(name) {
    return "Hello, " + name + "!";
};
console.log(greet("Bob"));
```
> **特点**：不能被提前调用（因为 `greet` 变量未初始化）

#### **（3）箭头函数（Arrow Function，ES6）**
```js
const greet = (name) => "Hello, " + name + "!";
console.log(greet("Charlie"));
```
> **特点**：更简洁，且**没有 `this` 绑定**

---

### **1.2 函数参数**
#### **（1）默认参数**
```js
function greet(name = "Guest") {
    return "Hello, " + name + "!";
}
console.log(greet()); // Hello, Guest!
```
#### **（2）剩余参数（Rest Parameter，ES6）**
```js
function sum(...numbers) {
    return numbers.reduce((acc, num) => acc + num, 0);
}
console.log(sum(1, 2, 3, 4)); // 10
```
> **适用于** 处理**不定数量**的参数

#### **（3）参数展开（Spread Operator，ES6）**
```js
const nums = [1, 2, 3];
console.log(sum(...nums)); // 6
```
> **适用于** 将数组**拆分**成多个参数

---

## **2. 作用域与闭包**
### **2.1 作用域（Scope）**
#### **（1）全局作用域**
```js
var globalVar = "I am global";
function showVar() {
    console.log(globalVar);
}
showVar(); // I am global
```

#### **（2）局部作用域**
```js
function test() {
    let localVar = "I am local";
    console.log(localVar); 
}
test(); // I am local
console.log(localVar); // ReferenceError: localVar is not defined
```

#### **（3）块级作用域（let/const）**
```js
{
    let x = 10;
}
console.log(x); // ReferenceError: x is not defined
```
> `var` 没有块级作用域，而 `let` 和 `const` 有；

---

### **2.2 闭包（Closure）**
先简单解释一下闭包，后续js进阶章节结合执行上下文、作用域链详细解释闭包；

理论上闭包：（MDN）闭包是指那些能够访问自由变量的函数，自由变量是指在函数中使用的，但既不是函数参数也不是函数的局部变量的变量；

从这个角度理解，岂不是所有的JavaScript函数都是闭包？下面引入实践意义上的闭包；

实践意义上闭包：（ECMAScript）即使创建自由变量的函数的上下文已经销毁，但该自由变量仍然存在，即该自由变量仍可被其他函数使用；

下面引入一个实践意义上闭包例子：

```js
function outer() {
    let count = 0; // 外部函数变量
    return function inner() { // 内部函数
        count++;
        console.log(count);
    };
}
const counter = outer(); // 这行代码解释：outer() 执行，返回 inner 函数给counter，此时，counter 是对 inner 函数的引用
counter(); // 1 // 这里counter()是对inner函数的调用
counter(); // 2
```
> **应用场景**：
> 1. **数据缓存**
> 2. **封装私有变量**
> 3. **计数器、定时器**

---

## **3. 高阶函数**
### **3.1 什么是高阶函数？**
**高阶函数（Higher-Order Function）** 是**接收函数作为参数**或**返回一个函数**的函数。

#### **（1）函数作为参数**
```js
function operate(a, b, callback) {
    return callback(a, b);
}

function add(x, y) {
    return x + y;
}

console.log(operate(3, 5, add)); // 8
```
> **应用场景**：回调函数（`map()`、`filter()`、`reduce()`）

#### **（2）返回一个函数**
```js
function multiplier(factor) {
    return function(number) {
        return number * factor;
    };
}
const double = multiplier(2);
console.log(double(5)); // 10
```
> **应用场景**：函数工厂、柯里化（Curry）
> 
> **函数工厂、柯里化？返回一个函数参数？上述代码？**

---

## **4. 常见高阶函数**
### **4.1 `map()`**
> **对数组中的每个元素进行处理，并返回新数组**
```js
const numbers = [1, 2, 3];
const squared = numbers.map(num => num * num);
console.log(squared); // [1, 4, 9]
```

### **4.2 `filter()`**
> **过滤符合条件的元素**
```js
const numbers = [1, 2, 3, 4, 5];
const evenNumbers = numbers.filter(num => num % 2 === 0);
console.log(evenNumbers); // [2, 4]
```

### **4.3 `reduce()`**
> **累加计算**
```js
const numbers = [1, 2, 3, 4];
const sum = numbers.reduce((acc, num) => acc + num, 0);
console.log(sum); // 10
```

---

## **5. 立即执行函数（IIFE）**
**立即执行函数（Immediately Invoked Function Expression，IIFE）** 在定义后立即执行，**避免污染全局作用域**：
```js
(function() {
    console.log("立即执行！");
})();
```

---

## **6. 函数柯里化（Currying）**
**柯里化** 是将一个多参数函数转换成一系列单参数函数：
```js
function curry(a) {
    return function(b) {
        return function(c) {
            return a + b + c;
        };
    };
}
console.log(curry(1)(2)(3)); // 6 // 从左至右调用对应从外到里函数传入值
```
> **应用场景**：函数复用、延迟计算

---

## **7. 函数防抖（Debounce）和节流（Throttle）**
### **7.1 函数防抖（Debounce）**
**作用**：防止函数在短时间内多次执行，只执行最后一次。
```js
function debounce(func, delay) {
    let timer;
    return function(...args) {
        clearTimeout(timer);
        timer = setTimeout(() => func.apply(this, args), delay);
    };
}

window.addEventListener("resize", debounce(() => console.log("窗口大小改变"), 500));
```
> **应用场景**：搜索输入框、窗口调整
>
> 内层function接收timer、外层函数func和delay参数，所以使用...args；
>
> timer变量作用？clearTimeout作用？func.apply(this,args)，apply用法？this指向？args?

### **7.2 函数节流（Throttle）**
**作用**：确保函数在一定时间间隔内只执行一次。
```js
function throttle(func, interval) {
    let lastTime = 0;
    return function(...args) {
        let now = Date.now();
        if (now - lastTime >= interval) {
            func.apply(this, args);
            lastTime = now;
        }
    };
}

document.addEventListener("scroll", throttle(() => console.log("页面滚动"), 1000));
```
> **应用场景**：滚动事件、按钮点击

