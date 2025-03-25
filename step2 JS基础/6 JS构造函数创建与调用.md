# **JavaScript 构造函数**
构造函数（Constructor Function）是用于**创建对象的特殊函数**，它可以批量生成具有相同属性和方法的对象。

---

## **1. 构造函数的概念**
- 构造函数本质上是一个**普通函数**，但用于创建对象。
- 命名惯例：**首字母大写**（如 `Person`）。
- 需要用 `new` 关键字调用，`this` 绑定到新创建的对象。

---

## **2. 创建和调用构造函数**
### **2.1 创建构造函数**
```js
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.sayHello = function() {
        console.log(`Hello, my name is ${this.name}`);
    };
}
```
### **2.2 通过 `new` 关键字调用**
```js
const p1 = new Person("Alice", 25);
const p2 = new Person("Bob", 30);

console.log(p1.name); // Alice
console.log(p2.age);  // 30
p1.sayHello(); // Hello, my name is Alice
```
**解析 `new` 过程：**
1. **创建一个新的空对象** `{}`。
2. **`this` 绑定到新对象**。
3. **执行构造函数代码**，给新对象添加属性和方法。
4. **返回新对象**（如果构造函数没有 `return`）。

---

## **3. 构造函数优化**
### **3.1 避免方法重复（使用 `prototype`）**
每次 `new` 一个对象时，方法都会重新创建，占用内存：
```js
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.sayHello = function() { // ❌ 每次 new 都会创建新方法
        console.log(`Hello, my name is ${this.name}`);
    };
}
```
**优化方法：使用 `prototype` 让所有实例共享方法**
```js
function Person(name, age) {
    this.name = name;
    this.age = age;
}

// 将方法定义在 `prototype` 上
Person.prototype.sayHello = function() {
    console.log(`Hello, my name is ${this.name}`);
};

const p1 = new Person("Charlie", 22);
const p2 = new Person("David", 28);
p1.sayHello(); // Hello, my name is Charlie
console.log(p1.sayHello === p2.sayHello); // ✅ true（方法被共享）
```

---

## **4. `new` 的模拟实现**
手写 `new` 关键字的执行过程：
```js
function myNew(constructor, ...args) {
    let obj = {}; // 1. 创建一个新对象
    obj.__proto__ = constructor.prototype; // 2. 关联原型
    let result = constructor.apply(obj, args); // 3. 执行构造函数
    return result instanceof Object ? result : obj; // 4. 返回新对象
}

function Animal(type) {
    this.type = type;
}
const cat = myNew(Animal, "Cat");
console.log(cat.type); // "Cat"
```

---

## **5. `class` 语法糖**
ES6 提供了 `class` 语法，更加清晰：
```js
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    sayHello() {
        console.log(`Hello, my name is ${this.name}`);
    }
}

const p1 = new Person("Eve", 35);
p1.sayHello(); // Hello, my name is Eve
```
**本质上还是使用 `function + prototype` 实现的。**

---

> ## **6. 结论**
> - 命名惯例：首字母大写；new关键词调用；
> - 使用protoytpe共享方法，代码示例？
> - new关键字的模拟实现，代码理解（学完原型与原型链再回头看）？
