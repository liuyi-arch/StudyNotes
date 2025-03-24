# **JavaScript `this` 规则与使用**
`this` 是 JavaScript 中的一个关键字，表示**当前执行上下文**，它的指向在不同的场景下会有所不同。

---

## **1. `this` 的五大规则**
`this` 的指向**取决于函数的调用方式**，主要有以下几种规则：
1. **默认绑定**（全局环境）
2. **隐式绑定**（对象调用）
3. **显示绑定**（`call` / `apply` / `bind`）
4. **`new` 绑定**（构造函数）
5. **箭头函数绑定**（继承外层 `this`）

---

## **2. `this` 详细规则**

### **2.1 默认绑定（非严格模式）**
如果 `this` 没有明确的绑定，它会指向 **全局对象（`window` / `globalThis`）**：
```js
function showThis() {
    console.log(this); // 在浏览器中：window
}
showThis();
```

**严格模式下（`use strict`）**，`this` 变为 `undefined`：
```js
"use strict";
function showThis() {
    console.log(this); // undefined
}
showThis();
```

---

### **2.2 隐式绑定（对象调用）**
当函数作为 **对象的方法** 调用时，`this` 指向**调用该方法的对象**：
```js
const obj = {
    name: "Alice",
    greet: function() {
        console.log(this.name); // this 指向 obj
    }
};
obj.greet(); // "Alice"
```

> **注意**：**丢失 `this`**
```js
const obj = {
    name: "Alice",
    greet: function() {
        console.log(this.name);
    }
};

const greetFn = obj.greet;
greetFn(); // ❌ undefined（this 变成了 window）
```
**解决方法**：使用 `.bind()` 绑定 `this`：
```js
const greetBind = obj.greet.bind(obj);
greetBind(); // "Alice"
```

---

### **2.3 显示绑定（`call` / `apply` / `bind`）**
#### **（1）`call()`**
使用 `call()` 显示绑定 `this`，传递参数：
```js
function showInfo(age) {
    console.log(this.name, age);
}

const person = { name: "Bob" };
showInfo.call(person, 25); // "Bob", 25
```

#### **（2）`apply()`**
`apply()` 作用与 `call()` 相同，但参数使用**数组**：
```js
showInfo.apply(person, [30]); // "Bob", 30
```

#### **（3）`bind()`**
`bind()` **不会立即执行**，返回一个新函数：
```js
const boundFn = showInfo.bind(person);
boundFn(35); // "Bob", 35
```

---

### **2.4 `new` 绑定（构造函数）**
当使用 `new` 调用构造函数时，`this` 绑定到**新创建的对象**：
```js
function Person(name) {
    this.name = name;
}
const p = new Person("Charlie");
console.log(p.name); // "Charlie"
```

---

### **2.5 箭头函数绑定（继承外层 `this`）**
箭头函数不会创建自己的 `this`，而是**继承外层作用域的 `this`**：
```js
const obj = {
    name: "David",
    greet: function() {
        setTimeout(() => {
            console.log(this.name); // 继承 obj 的 this
        }, 1000);
    }
};
obj.greet(); // "David"
```
**对比普通函数：**
```js
const obj = {
    name: "Eve",
    greet: function() {
        setTimeout(function() {
            console.log(this.name); // ❌ undefined（this 指向 window）
        }, 1000);
    }
};
obj.greet();
```
**总结：**
- 普通函数的 `this` 由**调用方式**决定
- **箭头函数的 `this` 继承外层**

---

## **3. `this` 在不同场景下的表现**
| 场景 | `this` 指向 |
|------|------------|
| **全局作用域**（非严格模式） | `window` / `globalThis` |
| **严格模式** | `undefined` |
| **对象方法** | 该对象 |
| **构造函数（`new`）** | 新创建的对象 |
| **`call` / `apply` / `bind`** | 绑定的对象 |
| **箭头函数** | 继承外层 `this` |

---

## **4. 实战案例**
### **4.1 修复 `this` 丢失问题**
```js
const obj = {
    name: "Grace",
    greet: function() {
        setTimeout(() => {
            console.log(this.name);
        }, 1000);
    }
};
obj.greet(); // "Grace"
```
**箭头函数保证 `this` 指向 `obj`，避免 `this` 变成 `window`。**

---

### **4.2 `this` 在事件处理程序中的使用**
```js
const button = document.querySelector("button");
button.addEventListener("click", function() {
    console.log(this); // 指向 `button`
});
```
**箭头函数无法绑定 `this`，此时 `this` 指向 `window`：**
```js
button.addEventListener("click", () => {
    console.log(this); // `window`，不是 `button`
});
```

---

### **4.3 `this` 在 class 中**
```js
class Person {
    constructor(name) {
        this.name = name;
    }

    greet() {
        console.log(`Hello, my name is ${this.name}`);
    }
}

const p = new Person("Henry");
p.greet(); // "Hello, my name is Henry"
```

**解决 `this` 丢失问题**
```js
class Person {
    constructor(name) {
        this.name = name;
    }

    greet() {
        setTimeout(() => {
            console.log(`Hello, my name is ${this.name}`); // 继承外层 `this`
        }, 1000);
    }
}
const p = new Person("Ivy");
p.greet(); // "Hello, my name is Ivy"
```

---

## **5. 总结**
1. **默认绑定**：`this` 指向 `window`（严格模式下 `undefined`）
2. **隐式绑定**：`this` 指向调用对象
3. **显示绑定**：`call()`、`apply()`、`bind()` 绑定 `this`
4. **`new` 绑定**：`this` 绑定到新对象
5. **箭头函数**：`this` 继承外层作用域

掌握 `this` 的规则，可以帮助我们更好地编写 JavaScript 代码，并解决 `this` 绑定错误的问题！🚀
