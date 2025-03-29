
## **1. Set（集合）**
`Set` 是一种**不允许重复元素**的数据结构，类似数组，但不能存储相同的值。  

### **特点**
✅ **值唯一**：不会存储重复的值。  
✅ **支持迭代**：可以使用 `forEach()`、`for...of` 遍历。  
✅ **提供基本操作方法**：`add()`、`delete()`、`has()`、`clear()`。  

### **示例**
```js
// 创建 Set
const mySet = new Set([1, 2, 3, 3, 4, 5, 5]);
console.log(mySet); // Set(5) {1, 2, 3, 4, 5}

// 添加新元素
mySet.add(6);
console.log(mySet); // Set(6) {1, 2, 3, 4, 5, 6}

// 删除元素
mySet.delete(3);
console.log(mySet); // Set(5) {1, 2, 4, 5, 6}

// 检查是否存在某个值
console.log(mySet.has(2)); // true
console.log(mySet.has(10)); // false

// 遍历 Set
mySet.forEach(value => console.log(value));
// 或者使用 for...of
for (let value of mySet) {
    console.log(value);
}

// 清空 Set
mySet.clear();
console.log(mySet); // Set(0) {}
```

### **应用场景**
- **去重数组**：利用 `Set` 自动去重  
  ```js
  const arr = [1, 2, 3, 3, 4, 4, 5];
  const uniqueArr = [...new Set(arr)];
  console.log(uniqueArr); // [1, 2, 3, 4, 5]
  ```
- **检查唯一性**：判断某个值是否存在，而不必遍历数组

---

## **2. Map（映射）**
`Map` 是一种键值对（key-value）结构，类似于对象，但它**允许任何类型的键（包括对象、函数等）**。  

### **特点**
✅ **键可以是任意类型**（对象、数组、函数等）  
✅ **有序存储**（按照插入顺序存储）  
✅ **提供 `size` 属性**（可获取长度）  
✅ **性能优于普通对象**（尤其在大量增删操作时）  

### **示例**
```js
// 创建 Map
const myMap = new Map();
myMap.set("name", "Alice");
myMap.set(1, "Number Key");
myMap.set({ key: "objectKey" }, "Object Key Value");

console.log(myMap);
// Map(3) {"name" => "Alice", 1 => "Number Key", Object => "Object Key Value"}

// 读取值
console.log(myMap.get("name")); // "Alice"
console.log(myMap.get(1)); // "Number Key"

// 检查是否存在键
console.log(myMap.has("name")); // true
console.log(myMap.has("age")); // false

// 删除键值对
myMap.delete(1);
console.log(myMap.has(1)); // false

// 获取 Map 的大小
console.log(myMap.size); // 2

// 遍历 Map
myMap.forEach((value, key) => console.log(key, value));
```
---

ES6 引入了许多新的方法，增强了 JavaScript 的功能和可读性。下面按照不同类型进行了分类整理，并提供了示例代码。

---

## **3. 数组（Array）相关新增方法**
| 方法 | 作用 |
|------|------|
| `Array.from()` | 将类数组或可迭代对象转换为数组 |
| `Array.of()` | 创建包含指定元素的新数组 |
| `find()` | 查找满足条件的**第一个**元素并返回 |
| `findIndex()` | 查找满足条件的**第一个**元素的索引 |
| `includes()` | 判断数组是否包含某个值 |
| `fill()` | 用指定值填充数组 |
| `copyWithin()` | 在数组内部复制一部分到另一个位置 |

### **示例**
```js
// Array.from() - 将类数组或可迭代对象转换为数组
console.log(Array.from("hello")); // ["h", "e", "l", "l", "o"]

// Array.of() - 创建数组
console.log(Array.of(1, 2, 3)); // [1, 2, 3]

// find() - 查找第一个符合条件的元素
let arr = [10, 20, 30, 40];
console.log(arr.find(num => num > 15)); // 20

// findIndex() - 查找第一个符合条件的元素索引
console.log(arr.findIndex(num => num > 15)); // 1

// includes() - 判断是否包含某个值
console.log(arr.includes(20)); // true

// fill() - 填充数组
console.log(arr.fill(0, 1, 3)); // [10, 0, 0, 40]

// copyWithin() - 复制数组的某部分到另一个位置
console.log(arr.copyWithin(1, 2, 4)); // [10, 0, 40, 40]
```

---

## **4. 数字（Number）相关新增方法**
| 方法 | 作用 |
|------|------|
| `Number.isFinite()` | 判断是否为有限数值 |
| `Number.isNaN()` | 判断是否是 `NaN` |
| `Number.isInteger()` | 判断是否是整数 |
| `Number.parseInt()` | 解析整数（类似 `parseInt`） |
| `Number.parseFloat()` | 解析浮点数（类似 `parseFloat`） |

### **示例**
```js
console.log(Number.isFinite(100)); // true
console.log(Number.isFinite(Infinity)); // false

console.log(Number.isNaN(NaN)); // true
console.log(Number.isNaN(123)); // false

console.log(Number.isInteger(5.5)); // false
console.log(Number.isInteger(10)); // true

console.log(Number.parseInt("42px")); // 42
console.log(Number.parseFloat("3.14em")); // 3.14
```

---

## **5. 字符串（String）相关新增方法**
| 方法 | 作用 |
|------|------|
| `startsWith()` | 判断字符串是否以指定内容开头 |
| `endsWith()` | 判断字符串是否以指定内容结尾 |
| `includes()` | 判断字符串是否包含指定内容 |
| `repeat()` | 复制字符串指定次数 |
| `padStart()` | 在字符串开头填充指定字符 |
| `padEnd()` | 在字符串末尾填充指定字符 |

### **示例**
```js
let str = "Hello, world!";

console.log(str.startsWith("Hello")); // true
console.log(str.endsWith("!")); // true
console.log(str.includes("world")); // true
console.log("Hi ".repeat(3)); // "Hi Hi Hi "
console.log("5".padStart(3, "0")); // "005"
console.log("5".padEnd(3, "0")); // "500"
```

---

## **6. 对象（Object）相关新增方法**
| 方法 | 作用 |
|------|------|
| `Object.assign()` | 复制对象属性（浅拷贝） |
| `Object.entries()` | 将对象转换为键值对数组 |
| `Object.values()` | 获取对象的所有值 |
| `Object.keys()` | 获取对象的所有键 |
| `Object.is()` | 严格比较两个值是否相等 |

### **示例**
```js
let obj = { a: 1, b: 2, c: 3 };

// Object.assign() - 复制对象
let newObj = Object.assign({}, obj, { d: 4 });
console.log(newObj); // { a: 1, b: 2, c: 3, d: 4 }

// Object.entries() - 转换为键值对数组
console.log(Object.entries(obj)); // [["a",1],["b",2],["c",3]]

// Object.values() - 获取所有值
console.log(Object.values(obj)); // [1, 2, 3]

// Object.keys() - 获取所有键
console.log(Object.keys(obj)); // ["a", "b", "c"]

// Object.is() - 严格比较
console.log(Object.is(NaN, NaN)); // true
console.log(Object.is(0, -0)); // false
```

---

## **7. Math 相关新增方法**
| 方法 | 作用 |
|------|------|
| `Math.trunc()` | 取整数部分 |
| `Math.sign()` | 判断数字的正负 |
| `Math.cbrt()` | 计算立方根 |
| `Math.hypot()` | 计算平方和的平方根 |

### **示例**
```js
console.log(Math.trunc(4.9)); // 4
console.log(Math.sign(-10)); // -1
console.log(Math.cbrt(27)); // 3
console.log(Math.hypot(3, 4)); // 5
```

---

## **8. Promise 相关新增方法**
| 方法 | 作用 |
|------|------|
| `Promise.resolve()` | 创建已成功的 Promise |
| `Promise.reject()` | 创建已失败的 Promise |
| `Promise.all()` | 并行执行多个 Promise，全部成功才返回 |
| `Promise.race()` | 并行执行多个 Promise，返回最先完成的结果 |

### **示例**
```js
let p1 = Promise.resolve("成功");
p1.then(console.log); // "成功"

let p2 = Promise.reject("失败");
p2.catch(console.log); // "失败"

let p3 = new Promise(resolve => setTimeout(() => resolve("P3 完成"), 1000));
let p4 = new Promise(resolve => setTimeout(() => resolve("P4 完成"), 2000));

Promise.all([p3, p4]).then(console.log); // ["P3 完成", "P4 完成"]

Promise.race([p3, p4]).then(console.log); // "P3 完成"
```

---

> ## **总结**
> Set不允许重复元素的数据结构，可用于数组去重；创建Set、添加、删除、是否存在某个值、清空Set代码示例；
> 键可以是任何类型、按插入顺序存储、size属性获取长度；读取值、是否存在键值对示例代码；
> - **数组**：`Array.from()`, `Array.of()`,`find(callback)`, `includes()`,`fill()`；
> - **字符串**：`startsWith()`, `repeat()`, `padStart()`；
> - **对象**：`Object.assign()`, `Object.entries()`,`Object.values()`,`Object.keys()`,`Object.is()`；
> - **Promise**：`Promise.all()`, `Promise.race()`,`Promise.resolve()`,`Promise.reject()`；
> - **Math**：`Math.trunc()`, `Math.sign()`；


