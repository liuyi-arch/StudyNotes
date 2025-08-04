## 一、浅谈this指向

结论：

- 全局作用域下调用：
  - 浏览器环境，指向window；
  - node环境，this指向module.exports；
    - Node.js 使用 CommonJS 模块系统，每个文件都是一个模块，module.exports是该文件模块顶层作用域；
- 普通函数内调用：
  - 非严格模式，this指向全局对象；具体细分，浏览器环境指向window，node环境指向global；
  - 严格模式，this两种环境都是指向undefined；
- 谁调用指向谁：
  - 函数作为某对象的方法被该对象调用：this指向该对象；
  - 构造函数调用：this指向新创建的实例对象；
- 显示绑定this，指向绑定的对象；
- 事件处理程序中this指向的是事件源；
- 特殊情况：
  - 箭头函数没有自己this，它沿着作用域链继承外层作用域的this；
  - 定时器处理函数、匿名函数指向window；
- 赋值或运算符导致 this 丢失；

优先级（由高到低）：

**`new` 构造函数 > `bind` 显式绑定 > `call` / `apply` 显式绑定 > 箭头函数 > 对象方法调用 > DOM 事件处理函数 > 定时器函数 > 自执行函数 > 普通函数直接调用**

验证：

```js
// 1. 全局作用域：浏览器环境、node环境
console.log(this === window); // true
console.log(this === module.exports); // true

// 2. 普通函数调用：非严格模式、严格模式
function test() {
    console.log(this === global); // true，node环境
    console.log(this === window); // true，浏览器环境
}
test();

'use strict'; // 放在文件的第一行，才能启用
function test() {
    console.log(this === undefined); // true
}
test();

// 3. 谁调用指向谁：函数作为某对象的方法被该对象调用
const obj = {
    name: "Tom",
    say() {
        console.log(this.name); // Tom
        console.log(this); // { name: 'Tom', say: [Function: say] }
    }
};

obj.say();

// 3. 谁调用指向谁：构造函数调用
function Person(name) {
    this.name = name;
}
const p = new Person("Alice");

console.log(p.name); // Alice
console.log(p); // Person { name: 'Alice' }
console.log(Person); // [Function: Person]

// 4. call、apply、bind显示绑定
function show() {
    console.log(this);
}

const obj = { name: "Tom" };

show.call(obj); // { name: "Tom" }
show.apply(obj); // { name: "Tom" }

const boundShow = show.bind(obj);
boundShow(); // { name: "Tom" }

// 5. 事件处理程序中this
// ...
var res = document.querySelector('div')

res.onclick = function () {
    console.log(this); //<div></div>
}
// ...

// 6. 特殊情况：箭头函数
const obj = {
    name: "Tom",
    show() {
        const inner = () => console.log(this.name); // Tom
        inner();

        function inner2() {
            console.log(this.name); // undefined
        }
        inner2();     
    }
};

obj.show();

// 6. 特殊情况：定时器处理函数、匿名函数
// 定时器处理函数
setTimeout(function() {
    console.log(this); //window
}, 1000)

// 自执行函数
(function() {
    console.log(this); //window
})()

// 7. 赋值或运算导致 this 丢失
const obj = {
    name: "Tom",
    say() {
        console.log(this.name);
    }
};

(obj.say)();  // Tom

const fn = obj.say;
fn(); // undefined，赋值
(false || obj.say)(); // undefined，运算
```

## 二、从Reference类型角度理解this指向

![image-20250713115642481](/Users/liuliu/Library/Application Support/typora-user-images/image-20250713115642481.png)

| 调用形式             | Reference 产生？         | this 指向          |
| -------------------- | ------------------------ | ------------------ |
| 普通函数调用         | 否                       | global / undefined |
| obj.方法()           | 是，Reference.base 为obj | obj                |
| new 调用             | 不适用                   | 实例对象           |
| call/apply/bind      | 不适用                   | 显式指定           |
| 箭头函数             | 否，继承外层Reference    | 外层环境           |
| 赋值后单独调用       | 否                       | 全局 / undefined   |
| (obj.foo, obj.foo)() | 否，丢失Reference        | 全局 / undefined   |

> The value of `this` is determined by the base value of the Reference that is the result of evaluating the MemberExpression
>
> ECMAScript 5.1 规范关于Reference链接：https://262.ecma-international.org/5.1/#sec-8
