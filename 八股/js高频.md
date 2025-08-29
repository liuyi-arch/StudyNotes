# js八股

## 1. call、apply、bind？✅

面试回答：

- call、apply、bind作用都是手动指定函数的this指向；第一个参数为该函数要指向的对象；

- call、apply、bind后续参数形式不同：call和bind为参数列表，apply为参数数组；

  ```js
  fn.call(obj, arg1, arg2, ...);
  fn.apply(obj, [arg1, arg2, ...]);
  ```

- call和apply是一次绑定且立即执行，bind是永久绑定，并且bind绑定优先级高于call和apply；

  ```js
  function sayName(){
    console.log(this.name);
  }
  const person = { name: 'zhangsan' };
  sayName.call(person); // 第一次绑定，输出zhangsan
  sayName(); // 第二次绑定，undefined
  
  const newSayName = sayName.bind(person);
  newSayName(); // 第一次绑定，输出zhangsan
  newSayName.call({ name: 'lisi' }); // 第二次绑定还是输出zhangsan，证明bind永久绑定，且优先级高于call,apply
  ```

## 2. this指向？

主要分为7种大的情况：

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
  - 箭头函数没有自己this，它沿着作用域链继承外层普通函数的this；
  - **定时器处理函数、匿名函数指向window**；
- 赋值或运算符导致 this 丢失；

优先级（由高到低）：

**`new` 构造函数 > `bind` 显式绑定 > `call` / `apply` 显式绑定 > 箭头函数 > 对象方法调用 > DOM 事件处理函数 > 定时器函数 > 自执行函数 > 普通函数直接调用**

下列解释深拷贝部分代码this指向：

```js
if (typeof obj === 'function') {
    return function (...args) {
        return obj.call(this, ...args); // 拷贝函数本体功能和保留this的动态绑定特性（即谁调用指向谁），而不拷贝函数运行时的内存结构，比如函数的闭包上下文；
    }
}

// obj.call(this , ...args)，将绑定到调用该函数的对象上；
// obj.call(...args)，call没有设置第一个参数，那么将指向全局；
```

## 3. var let const?✅

| 特性             | var                         | let                                  | const                                |
| ---------------- | --------------------------- | ------------------------------------ | ------------------------------------ |
| 作用域           | 函数作用域                  | 块级作用域                           | 块级作用域                           |
| 变量提升         | 有提升，声明前访问undefined | 有提升，声明前访问ReferenceError错误 | 有提升，声明前访问ReferenceError错误 |
| 暂时性死区 (TDZ) | 无                          | 有                                   | 有                                   |
| 重复声明         | 可以                        | 不可以                               | 不可以                               |
| 重新赋值         | 可以                        | 可以                                 | 不可以                               |

关于const使用场景误区：

```js
// 错误
for(const i = 0;i < 10;i++){
    console.log(i);
}
// 正确
for (const key in obj) {
    console.log(key);
}
```

- for循环中，每轮循环i被重新赋值，是同一个i；
- for...in或for...of循环中，每次迭代都是一个新的key；

（补充）关于提升：函数声明提升，函数表达式不提升；还有诸如class提升、import提升；

详细见：https://github.com/liuyi-arch/StudyNotes/blob/main/step2%20Javascript/1%20%E5%8F%98%E9%87%8F%E5%A3%B0%E6%98%8E%E4%B8%8E%E6%8F%90%E5%8D%87.md

## 4. 原型与原型链

**1. `prototype、__proto__、constructor`：**

- prototype：构造函数有的属性；
- `__proto__`实例对象内部的[[prorotype]]属性；
- constructor：每个prototype对象都有一个constructor属性，可以指回原构造函数；

关系：

```js
person.__proto__ === Person.prototype;
Person.prototype.constructor === Person;
```



**2. 原型链：**访问某个对象的属性或方法时，如果该对象自身没有，那么会延着`__proto__`向上查找；

关系：

```js
person.__proto__ === Person.prototype;
Person.prototype.__proto__ === Object.prototype;
Object.prototype.__proto__ === null;
```

**3. 重写原型对象，要手动将constructor补回来：**

```js
// 会丢掉constructor属性
function Person(name) {
    this.name = name;
}
Person.prototype = {
    sayHi() {
        console.log(`Hi, I'm ${this.name}`);
    }
};

// 正确写法
function Person(name) {
    this.name = name;
}
Person.prototype = {
    constructor: Person, // 手动补回
    sayHi() {
        console.log(`Hi, I'm ${this.name}`);
    }
};
```

**4. `__proto__`是非标准属性，现代写法使用`Object.getPrototypeOf(obj)`代替**，见如下示例：

```js
// 示例1
console.log(Person.prototype.__proto__.name);
console.log(Object.getPrototypeOf(Person.prototype).name);

// 示例二
console.log(p1.__proto__.name);
console.log(Person.getPrototypeOf(p1).name);
```

## 5. Promise理解？✅

背景：Promise之前，如果要处理异步问题，需要层层callback function；

```js
setTimeout(function () {
    console.log("1");
    setTimeout(function () {
        console.log("2");
        setTimeout(function () {
            console.log("3");
        }, 3000);
    }, 2000);
}, 1000);
```

下面将使用Promise链、async/await改写上述callback function：

```js
const sleep = (ms) => new Promise(resolve => setTimeout(resolve, ms));

// Promise链
sleep(1000)
  .then(() => {
    console.log("1");
    return sleep(2000);
  })
  .then(() => {
    console.log("2");
    return sleep(3000);
  })
  .then(() => {
    console.log("3");
  })
  .catch(err => {
    console.error('出错了：', err);
  });

// async/await
async function run() {
  try {
    await sleep(1000); // sleep()宏任务
    console.log("1"); // await后的console.log()微任务

    await sleep(2000);
    console.log("2");

    await sleep(3000);
    console.log("3");
  } catch (err) {
    console.error('出错了：', err);
  }
}

run();
```



Promise概念：

- 核心作用：用来处理异步操作，避免回调地狱，支持链式调用；

- 是一个构造函数，接受一个函数作为参数，返回一个 Promise 实例；

- 有以下特点：

  - **状态不可逆**：一旦从 **`pending` 变成 `fulfilled` 或 `rejected`**，就不能再改变。

  - **then/catch 总是异步执行**：即使 Promise 已经是 fulfilled 状态，回调也会进入 **微任务队列**。

  - **值穿透**：如果 `then()` 没有返回值，会自动将上一个值传递下去。

  - **错误冒泡**：未捕获的错误会沿着链条冒泡到最近的 `catch()`。



使用：

- resolve(xx)作用是将Promise的状态从**pending改变成fufilled**，触发 then 的第一个回调；
  - **`resolve()`**：状态变为 fulfilled，`then` 会被触发，**回调的参数是 `undefined`**；
  - **`resolve(value)`**：状态变为 fulfilled，**`then` 的回调会接收到 `value`**；
  - 如果**不使用resolve()**，状态仍为pending，**无法触发then**；
- reject(xx)作用是将Promise的状态从**pending改变成rejected**，触发 then 的第二个回调（不常用，推荐用 `catch`）或 catch ；
- **pending状态then、catch、finally都不能触发**；

```js
const p = new Promise((resolve, reject) => {
    setTimeout(() => {
        const success = true;
        if (success) {
            // resolve(); // then中输出‘成功：undefined’
            // resolve("数据加载成功"); // then中输出‘成功：数据加载成功’
            console.log("数据加载成功"); // then、catch、finally中都不会输出
        } else {
            // reject("数据加载失败");
            reject();
        }
    }, 1000);
});

p.then(result => {
    console.log("成功:", result);
}).catch(error => {
    console.error("失败:", error);
}).finally(() => {
    console.log("无论成功还是失败都会执行"); // 需要执行resolve或reject才会执行
});
```



async/await：

- **then 和 await 在本质上都是等待 Promise resolve 后再执行下一个**；
- 使用try/catch语法包裹await，错误全部在catch中抛出，代码结构比then更像同步；



Promise常用API：

- `then(onFulfilled, onRejected)` → 处理成功与失败

- `catch(onRejected)` → 处理失败

- `finally(onFinally)` → 无论结果都会执行

- **`Promise.all(iterable)`** → 全部成功才成功，任一失败即失败

- **`Promise.race(iterable)`** → 最快的一个决定结果

- **`Promise.allSettled(iterable)`** → 等待所有完成，返回每个结果状态数组

## 6. js有哪些数据类型？判断数据类型方法？✅

基本数据类型：**Number、String、Boolean、null、undefined、BigInt、Symbol**；

复杂数据类型：**Object、Array、Function、Date、RegExp、Map、Set**、WeakMap、WeakSet等；

> 1. Synbol类型有两种创建方式：Symbol()、Symbol.for()，其中Symbol()创建的值是唯一的，Symbol.for()创建的值是共享的；
> 2. Symbol类型作为键使用时，不会被for...in等方式访问到；



数据类型检测：

- `typeof xx`：设计缺陷，对null判断是object；
- **`Object.prototype.toString.call(xx)`**：最准确，适用所有数据类型；
- `person instanceof Person`：判断实例对象是否是构造函数的实例；



数据类型赋值：

- 基本数据类型存储的是实际值、复杂数据类型存储的是引用地址；

- 复杂数据类型深浅拷贝：

  - 浅拷贝：**扩展运算符`[...arr]、{...obj}`**

    ```js
    let obj = { a: 1, b: { c: 2 } };
    let shallow = { ...obj };
    console.log(shallow); // { a: 1, b: { c: 2 } }
    
    const res = [];
    const path = [1, 2];
    // res.push(...path); // [1, 2]
    res.push(path); // [[1, 2]]
    console.log(res);
    ```

  - 深拷贝：

    - `**JSON.parse(JSON.stringify(xx))**`：不能拷贝 `undefined`、`function`、`Symbol`、循环引用；
    - **`structuredClone(xx)`**



数据类型转换：

- 隐式转换：
  - 字符串拼接，比如1 + '2' -> '12'；
  - 字符串、boolean转化为数字，比如'2' - 1 -> 1、1 + true -> 2等；
- 显式转换：
  - 转换为数字：Number(xx)、parseInt(xx)、parseFloat(xx)等；
  - 转换为字符串：String(xx)、xx.toString()等；
  - 转换为boolean值：Boolean(xx)；

## 7. js常用数组方法？哪些数组方法不改变原数组？✅

| 类别     | 方法                                       | 作用                               | 示例                                 |
| -------- | ------------------------------------------ | ---------------------------------- | ------------------------------------ |
| **增**   | `push()`                                   | 向数组末尾添加元素，**返回新长度** | `arr.push(4)` → `[1,2,3,4]`          |
|          | `unshift()`                                | 向数组开头添加元素，**返回新长度** | `arr.unshift(0)` → `[0,1,2,3]`       |
|          | `concat()`                                 | 合并数组，返回新数组               | `[1,2].concat([3,4])` → `[1,2,3,4]`  |
| **删**   | `pop()`                                    | 删除末尾元素，**返回被删元素**     | `[1,2,3].pop()` → `3`                |
|          | `shift()`                                  | 删除开头元素，**返回被删元素**     | `[1,2,3].shift()` → `1`              |
|          | **`splice(start, deleteCount)`**           | **删除指定位置**元素               | `arr.splice(1,1)` → 删除第2个元素    |
| **改**   | **`splice(start, deleteCount, ...items)`** | **删除并插入新元素**               | `arr.splice(1,1,'x')` → 替换         |
|          | **`fill(value, start, end)`**              | 用**指定值填充数组**               | `[1,2,3].fill(0,1,3)` → `[1,0,0]`    |
|          | `sort()`                                   | 排序（默认按字符串）               | `[3,1,2].sort()` → `[1,2,3]`         |
|          | `reverse()`                                | 数组反转                           | `[1,2,3].reverse()` → `[3,2,1]`      |
| **查**   | `indexOf()`                                | 查找元素下标                       | `[1,2,3].indexOf(2)` → `1`           |
|          | **`lastIndexOf()`**                        | **从后往前查找下标**               | `[1,2,3,2].lastIndexOf(2)` → `3`     |
|          | **`includes()`**                           | 是否包含某元素                     | `[1,2,3].includes(2)` → `true`       |
|          | **`find()`**                               | **找到符合条件的第一个元素**       | `[1,2,3].find(x=>x>1)` → `2`         |
|          | **`findIndex()`**                          | **找到符合条件的下标**             | `[1,2,3].findIndex(x=>x>1)` → `1`    |
| **遍历** | `forEach()`                                | 遍历数组（**无返回值**）           | `[1,2,3].forEach(x=>console.log(x))` |
|          | `map()`                                    | 遍历并**返回新数组**               | `[1,2,3].map(x=>x*2)` → `[2,4,6]`    |
|          | **`filter()`**                             | 过滤数组                           | `[1,2,3].filter(x=>x>1)` → `[2,3]`   |
|          | **`every()`**                              | 所有元素是否满足条件               | `[2,4,6].every(x=>x%2===0)` → `true` |
|          | **`some()`**                               | 是否有元素满足条件                 | `[1,3,5].some(x=>x%2===0)` → `false` |
|          | `reduce()`                                 | 累积计算                           | `[1,2,3].reduce((a,b)=>a+b,0)` → `6` |
| **转换** | **`join()`**                               | 数组转字符串                       | **`[1,2,3].join('-') // "1-2-3"`**   |
|          | **`toString()`**                           | 转换为字符串                       | **`[1,2,3].toString() // "1,2,3"`**  |

| **截取** | **`slice(start, end)`**                                 | **截取[m , n)（不修改原数组）；支持负数索引（-1 表示最后一个元素）** | `[1,2,3,4].slice(1,3)` → `[2,3]`,`[1,2,3,4].slice(-4,-1)`→`[1,2,3]` |
| -------- | ------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
|          |                                                         | 应用                                                         | **数组浅拷贝：`const copy = arr.slice()`**                   |
|          | **`arr.splice(start, deleteCount, item1, item2, ...)`** | **两个参数：截取[m , n]（会修改原数组）**                    | `[1, 2, 3, 4, 5].splice(2,2)` →`[3,4]`                       |
|          |                                                         | **`四个参数，插入元素`**                                     | `[1, 2, 5].splice(2,0,6,7)`→`[1,2,6,7,5]`                    |
|          |                                                         | **`四个参数，替换元素`**                                     | `[1, 2, 6, 7, 5].splice(1,2,8,9)`→`[1, 8, 9, 7, 5]`          |

- 会改变原数组：增、删、改、转换、splice()、sort()、reverse()、fill()、map()中arr为复杂数据类型；
  - push()、unshift()、pop()、shift()、splice()、fill()、sort()、reverse()、**map()中arr为复杂数据类型**；
- 不会改变原数组：查、遍历、map()中arr为基本数据类型、转换、concat()、slice()；
  - IndexOf()、includes()、find()、findIndex()、forEach()、filter()、every()、some()、reduce()、join()、toString()、concat()、slice()、map()中arr为基本数据类型；

## 9. ES6新特性？✅

1. 新的声明方式const、let：块级作用域、存在变量提升，但存在暂时性死区、let/const不能重复声明，const不能重复赋值；
2. 新的数据结构：Set()不重复集合、Map()键值对，键可以是任何类型；
3. 新的数据类型Symbol()：唯一的值，常用于私有属性；Symbol.for()共享值；作为键使用不会被for...in访问到；
4. 新的遍历方式：for...of用于数组，for...in用于对象；
5. 箭头函数：没有this绑定，继承外层函数的this、**不能用作构造函数，没有prototype、没有arguments对象**；
6. 展开运算符：可用于浅拷贝、合并数组或对象；输出dp数组最大值`Math.max(...dp)`？
7. Promise；
8. 类class与继承：super()与extends；
9. 模版字符串${}、参数默认值、解构赋值、对象字面量；

## 10. 箭头函数？✅

特点：

- 无function，无函数名；



与普通函数比较：

- 箭头函数没有自己this(因此也不能call/apply/bind绑定)，它沿着作用域链继承外层普通函数的this，如果**外层没有普通函数，那么继承全局作用域this**(严格模式指向undefined，非严格模式指向window)；

  - 而普通函数this指向取决于谁调用它；

  ```js
  const obj = {
    value: 42,
    normal: function() {
      console.log(this.value);
    },
    arrow: () => {
      console.log(this.value);
    }
  };
  
  obj.normal(); // 42 (this -> obj)
  obj.arrow();  // undefined (this -> 外层作用域，全局)
  ```

- 不可用做构造函数、没有prototype属性、没有arguments对象（保存实参列表，arguments现已被剩余参数取代）；

  - 类继承必须先super()再使用this；



使用场景：

- 适用于回调函数，比如定时器；

- 不适用于需要动态绑定的**方法**；

  ```js
  // 例1:用箭头函数
  class Person {
    constructor(name) {
      this.name = name;
    }
  
    startTalking() { 
      setInterval(() => { 
        console.log(this.name);
      }, 1000);
    }
  }
  
  const p = new Person("Alice");
  p.startTalking();
  
  
  // 例2:不适用于箭头函数
  const obj = {
    name: "Alice",
    say: () => {
      console.log(this.name);
    }
  };
  
  obj.say(); // undefined（因为 this 外层没有普通函数，则指向 window，而不是 obj）
  ```

  例1中：

  - 使用箭头函数，this指向外层函数，即Person(构造函数也是函数)，那么p.startTalking(),this指向Person的实例对象p；
  - 使用普通函数，那么定时器中this指向的是全局；

## 11. 闭包？

词法作用域：取决于函数定义时所处的环境，而不是调用位置；

Javascript使用的是词法作用域；

> 动态作用域中，函数内部的变量查找是从调用它的位置向上查找；

```js
var value = 1;

function foo() {
    console.log(value); // 1
}

function bar() {
    var value = 2;
    foo();
}

bar();
```



闭包ECMAScript定义：即使创建词法环境的执行上下文不在活跃，外层引用仍然可能保留；

自己理解：函数返回后仍然可以访问其定义时的词法作用域；



闭包好处：变量私有化、持久化变量；

闭包缺点：长期占用内存；

## 12. 事件循环机制？宏任务/微任务？✅

- 什么是事件循环机制？存在意义是什么？
  - JS 是单线程语言，因此事件循环存在的意义是用来处理异步任务的。



- 同步任务：立即执行，进入 主线程的调用栈；
- 异步任务：不立刻执行，而是交给 **宿主环境（浏览器/Node.js）** 处理，完成后再把回调加入 **任务队列**；



- 常见宏任务与微任务：
  - 宏任务：**整个`<script>`脚本**、setTimeout/setInterval、**postMessage、I/O（涉及外部设备交互的操作）、用户交互事件（点击、键盘事件等）**；
  - 微任务：**Promise.then/catch/finally**等；
  - 优先级：**同步任务高于异步任务（宏任务、微任务）；微任务高于宏任务**；



- 示例：

```js
console.log("1");

setTimeout(() => { // 宏任务中放微任务
    console.log("2");
  
    Promise.resolve().then(() => {
      console.log("3");
    });
  }, 0);

Promise.resolve().then(() => { 
  console.log("4");
  setTimeout(() => { // 微任务中放宏任务
    console.log("5");
  }, 0);
}).then(() => {
  console.log("6");
});

console.log("7");
// 输出结果：1 -> 7 -> 4 -> 6 -> 2 -> 3 -> 5
```
