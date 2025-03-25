### **JavaScript 变量声明与提升机制**  

在 JavaScript 中，变量可以使用 `var`、`let` 和 `const` 进行声明，并且 JavaScript 存在变量提升（Hoisting）的机制，使得变量在执行前已经被解析。

---

## **1. 变量声明方式**
JavaScript 提供了三种变量声明方式：
| 关键字 | 作用 | 作用域 | 变量提升 | 赋值前访问 |
|--------|------|--------|---------|-----------|
| `var` | 变量声明 | **函数作用域** | **会提升**（提升到作用域顶部，值为 `undefined`） | `undefined` |
| `let` | 块级作用域变量 | **块级作用域** | **会提升**（但不会初始化） | `ReferenceError` |
| `const` | 常量声明（不可修改） | **块级作用域** | **会提升**（但不会初始化） | `ReferenceError` |

---

## **2. 变量提升（Hoisting）**
变量提升指的是：**JavaScript 在代码执行前，会将 `var` 变量声明和 `function` 函数声明提升到当前作用域的顶部**。  
但 **`let` 和 `const` 变量虽然提升了，但不会被初始化（TDZ 暂时性死区）**。

### **2.1 `var` 的变量提升**
```js
console.log(a); // 输出 undefined
var a = 10;
console.log(a); // 输出 10
```
实际执行过程：
```js
var a;  // 变量声明被提升
console.log(a); // undefined
a = 10; 
console.log(a); // 10
```
`var` 变量提升后，会被默认赋值 `undefined`，但**不会提升赋值部分**。

---

### **2.2 `let` 和 `const` 的变量提升**
```js
console.log(b); // ❌ ReferenceError: Cannot access 'b' before initialization
let b = 20;
```
```js
console.log(c); // ❌ ReferenceError: Cannot access 'c' before initialization
const c = 30;
```
虽然 `let` 和 `const` 也会提升，但它们不会初始化，导致在初始化前访问会报 `ReferenceError`（暂时性死区，TDZ）。

---

## **3. 变量提升的执行过程**
### **3.1 `var` 的提升**
```js
function test() {
  console.log(x); // undefined
  var x = 5;
  console.log(x); // 5
}
test();
```
**执行步骤**：
1. **编译阶段**：`var x;` 被提前提升，但不会赋值。
2. **执行阶段**：
   - `console.log(x);` 输出 `undefined`
   - `x = 5;` 赋值
   - `console.log(x);` 输出 `5`

等价于：
```js
function test() {
  var x; // 提升
  console.log(x); // undefined
  x = 5;
  console.log(x); // 5
}
```

---

### **3.2 `let` 的提升（TDZ 暂时性死区）**
```js
function test() {
  console.log(y); // ❌ ReferenceError
  let y = 10;
}
test();
```
`let` 变量在**进入作用域后即“存在”，但在初始化前不能访问**，称为 **暂时性死区（Temporal Dead Zone, TDZ）**。

等价于：
```js
function test() {
  // TDZ 开始
  let y; // 变量声明提升，但未初始化
  console.log(y); // ❌ ReferenceError: Cannot access 'y' before initialization
  y = 10; // 变量初始化
}
```
🔹 **结论**：`let` 变量虽然提升了，但不会初始化，因此在访问前会抛出 `ReferenceError`。

---

### **3.3 `const` 的提升**
```js
function test() {
  console.log(z); // ❌ ReferenceError
  const z = 15;
}
test();
```
**特点**：
- `const` 变量与 `let` 类似，也存在 TDZ，不能在初始化前访问。
- `const` 变量必须在声明时赋值，否则会报错。

```js
const PI; // ❌ SyntaxError: Missing initializer in const declaration
```

---

## **4. 函数提升**
**函数声明**会整体提升，而**函数表达式**不会。
```js
foo(); // ✅ 正常执行
function foo() {
  console.log("Hello");
}
```
**等价于**
```js
function foo() { console.log("Hello"); }
foo();
```
**函数表达式不会提升**：
```js
bar(); // ❌ TypeError: bar is not a function
var bar = function() { console.log("Hello"); };
```
**原因**：
- `bar` 被 `var` 声明提升，但它的值 `function() {...}` 不会提升。

等价于：
```js
var bar;
bar(); // ❌ TypeError
bar = function() { console.log("Hello"); };
```

---

## **5. 变量提升的底层原理**
### **JavaScript 代码执行过程**
JavaScript 执行代码时分 **两步**：
1. **编译阶段（创建执行上下文）**
   - 变量和函数声明会被提升。
   - `var` 变量会被默认初始化为 `undefined`。
   - `let` 和 `const` 变量不会初始化（进入 TDZ）。
   - `function` 函数会完整提升。
  
2. **执行阶段**
   - 按照代码顺序执行变量和函数赋值操作。

---

## **6. 变量提升总结**
| 关键字 | 作用域 | 变量提升 | 初始值 | 是否进入 TDZ |
|--------|--------|---------|-------|-------------|
| `var` | 函数作用域 | **提升**（提升到作用域顶部） | `undefined` | ❌ |
| `let` | 块级作用域 | **提升**（但不会初始化） | ❌ `ReferenceError` | ✅ |
| `const` | 块级作用域 | **提升**（但不会初始化） | ❌ `ReferenceError` | ✅ |
| `function` | 函数作用域 | **完整提升** | 函数体 | ❌ |

### **最佳实践**
- **尽量使用 `let` 和 `const`** 避免 `var` 变量提升导致的 `undefined`。
- **避免在变量声明前访问变量**，防止 `TDZ` 错误。
- **使用 `const` 声明不会变的变量**，防止意外修改。

---

> 变量提升var、let、const是否提升？初始值？
>
> 函数提升，函数声明、函数表达式是否提升？其示例代码？
