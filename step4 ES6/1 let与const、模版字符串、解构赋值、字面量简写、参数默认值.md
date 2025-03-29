
## **1. 变量声明（`let` 和 `const`）**
ES6 引入了 `let` 和 `const` 关键字，用于替代 `var`，增强变量的作用域管理。

### **1.1 `let` 关键字**
- **块级作用域（{} 内有效）**
- **不允许重复声明，但可重新赋值**

```js
let name = "Alice";
name = "Bob"; // ✅ 允许修改

if (true) {
    let age = 25;
}
console.log(age); // ❌ 报错，age 只在 if 代码块内有效
```

### **1.2 `const` 关键字**
- **块级作用域**
- **声明时必须赋值，且不能修改；对象内部属性可变**

```js
const PI = 3.14;
PI = 3.1415; // ❌ 报错，const 变量不可重新赋值
```

---

## **2. 模板字符串（Template Literals）**
| 特性 | 传统字符串 | 模板字符串 |
|------|------|------|
| **字符串拼接** | `str1 + " " + str2` | `` `${str1} ${str2}` `` |
| **多行字符串** | `line1 + "\n" + line2` | `` `line1 \n line2` `` |
| **嵌入变量** | `name + " is " + age + " years old"` | `` `${name} is ${age} years old` `` |
| **表达式支持** | 需要拼接 | `` `${5 + 3}` `` |
| **函数调用** | 需要拼接 | `` `${greet(name)}` `` |引号（``）** 语法，支持多行字符串和变量插值。

```js
let name = "Alice";
let age = 25;
console.log(`Hello, my name is ${name} and I am ${age} years old.`);

```
---

## **3. 解构赋值（Destructuring）**

| 解构方式 | 语法 | 示例 |
|---------|------|------|
| **数组解构** | `const [a, b] = array;` | `[x, y] = [10, 20]` |
| **跳过值** | `const [a, , c] = array;` | `[10, , 30]` |
| **默认值** | `const [a = 10] = array;` | `[undefined] -> a = 10` |
| **交换变量** | `[a, b] = [b, a];` | `x=1, y=2 -> x=2, y=1` |
| **对象解构** | `const {a, b} = obj;` | `{name, age} = {name: "Alice", age: 25}` |
| **变量重命名** | `const {a: x} = obj;` | `{name: userName} = {name: "Alice"}` |
| **嵌套解构** | `const { a: { b } } = obj;` | `{ address: { city, zip } }` |
| **rest 参数** | `const [...rest] = array;` | `[first, ...rest] = [1,2,3]` |

### **3.1 数组解构**
```js
const numbers = [1, 2, 3];
const [a, b, c] = numbers;
console.log(a, b, c); // 1 2 3
```

### **3.2 对象解构**
```js
const person = { name: "Alice", age: 25 };
const { name, age } = person;
console.log(name, age); // Alice 25
```

---

## **4.对象字面量的简洁与灵活表达方式（ES6+）**
### **4.1 属性简写（Shorthand Property Names）**
  
当对象的 **键名** 与 **变量名** 相同时，可以使用 **属性简写** 省略 `key: value` 的重复写法。
  
```js
const name = "Alice";
const age = 25;

// 传统写法
const person1 = {
    name: name,
    age: age
};

// ES6 属性简写
const person2 = { name, age };

console.log(person2); // { name: "Alice", age: 25 }
```
---

### **4.2 方法简写（Shorthand Method Names）**
在对象中定义方法时，可以去掉 `function` 关键字和冒号 `:`。

```js
const person = {
    name: "Alice",
    
    // 传统写法
    sayHello: function() {
        console.log("Hello, I am " + this.name);
    },

    // ES6 方法简写
    sayHi() {
        console.log("Hi, I am " + this.name);
    }
};

person.sayHello(); // Hello, I am Alice
person.sayHi();    // Hi, I am Alice
```

---

### **4.3 计算属性名（Computed Property Names）**
ES6 允许在对象 **键名** 处使用 `[]` 计算属性的值，使对象的属性名更灵活。

```js
const key = "age";
const person = {
    name: "Alice",
    [key]: 25  // 计算属性名
};

console.log(person.age); // 25
```
**动态生成多个属性**
```js
const prefix = "user_";
const user = {
    [prefix + "id"]: 1,
    [prefix + "name"]: "Alice"
};
```

## **5.参数默认值的使用（ES6+）**

| **特性** | **示例** | **作用** |
|---------|---------|---------|
| **基本用法** | `function greet(name = "Guest") {}` | 设定默认值，避免 `undefined` |
| **解构赋值默认值** | `function displayUser({ name = "Guest" } = {}) {}` | 适用于缺少对象属性的情况 |
| **结合 Rest 参数** | `function sum(a = 0, ...rest) {}` | 适用于变长参数的场景 |
| **`undefined` 触发默认值** | `test(undefined); // 使用默认值` | 仅 `undefined` 触发，`null` 不触发 |
| **计算表达式** | `function greet(name = getName()) {}` | 允许使用动态计算的默认值 |
| **箭头函数默认值** | `const multiply = (a = 1, b = 2) => a * b;` | 适用于简写函数 |

