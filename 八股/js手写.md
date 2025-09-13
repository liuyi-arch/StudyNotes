# js手写

## 1. ✅防抖、节流

- 防抖：**在事件被触发n秒后再执⾏回调函数。如果在这段时间内⼜触发了该事件，则重新计时**。⽐如，在输⼊框搜索时，⽤户连续输⼊字符，我们不希望每次输⼊都触发搜索请求，⽽是在⽤户停⽌输⼊⼀段时间后再触发搜索请求。

- 节流：在**每隔n秒后执⾏⼀次回调函数**。⽐如，当⽤户滚动⻚⾯时，我们不希望每次滚动都触发相应的操作，⽽是每隔⼀段时间才执⾏⼀次操作。
- 注意：防抖节流函数优化版本都是首次立即执行。

P74-78

```js
// 防抖函数
function debounce(fn, delay = 300, immediate = false) {
    let timer = null;       
    let invoked = false;    

    return function (...args) {
        const context = this; 
        if (timer) clearTimeout(timer); // 有正在执行的定时器，清除
      
        if (immediate && !invoked) {     
            fn.call(context, ...args);  
            invoked = true;              
        } else {                         
            timer = setTimeout(() => {
                fn.call(context, ...args);
                invoked = false;           
            }, delay);
        }
    }
}

const log = debounce((msg) => {
    console.log(msg);
}, 1000, true);

log("第一次调用"); // 立即执行
setTimeout(() => log("第二次调用"), 200); // 1200ms 内有后续调用，不执行
setTimeout(() => log("第三次调用"), 1200); 
```

```js
// 节流函数
function throttle(fn, interval = 500){
  let timer = null;
  let firstTime = true;
  
  return function(...args){
    const context = this;
    if(timer) return false; // 有正在执行定时器，返回
    
    if(firstTime){
      fn.call(context, ...args);
      return firstTime = false;
    };
    
    timer = setTimeout(function(){
      timer = null;
      fn.call(context, ...args);
    }, interval);
  }
}

const log = throttle((msg) => {
  console.log(msg);
}, 500);

let count = 0;
const timer = setInterval(() => {
  log('第' + (++count) + '次调用');
  if(count > 20) clearInterval(timer);
}, 50);
```

## 2. ✅深浅拷⻉

P79-81

```js
// 浅拷贝
function shallow(obj) {
    const newObj = {};
    for (const key in obj) {
        newObj[key] = obj[key];
    }
    return newObj;
}

// 深拷贝
function deepCopy(obj, cache = new WeakMap()) {
    // 处理原始类型和 null
    if (obj === null || typeof obj !== 'object') return obj

    // 处理特殊对象
    if (obj instanceof Date) return new Date(obj)
    if (obj instanceof RegExp) return new RegExp(obj)
    if (obj instanceof Error) return new Error(obj.message)
    if (typeof obj === 'function') {
        return function (...args) {
            return obj.apply(this, args)
        }
    }

    // 处理循环引用
    if (cache.has(obj)) return cache.get(obj)

    // 处理 Map
    if (obj instanceof Map) {
        const newObj = new Map();
        cache.set(obj, newObj);
        obj.forEach((value, key) => { // 注意forEach中回调value在前，key在后
            newObj.set(deepCopy(key, cache), deepCopy(value, cache));
        })
        return newObj;
    }

    // 处理 Set
    if (obj instanceof Set) {
        const newObj = new Set();
        cache.set(obj, newObj);
        obj.forEach(value => {
            newObj.add(deepCopy(value, cache));
        })
        return newObj;
    }

    // 普通对象/数组
    const newObj = Array.isArray(obj) ? [] : {}
    cache.set(obj, newObj)

    // 支持 Symbol 属性
    const keys = Reflect.ownKeys(obj)
    for (const key of keys) {
        newObj[key] = deepCopy(obj[key], cache)
    }

    // for (let key in obj) { // for...in只能遍历对象自身和原型链上可枚举的属性，Symbol默认是不可枚举的
    //     if (Object.hasOwn(key)) { // 因此该方法不支持Symbol属性
    //         newObj[key] = deepCopy(obj[key], cache);
    //     }
    // }

    return newObj;
}
```

深拷贝API方案：

- **`newObj = JSON.parse(JSON.stringify(obj))`**

  - 适用**数字、字符串、布尔值、数组和普通对象**；

- `clonedObj = structuredClone(originalObj)`

  - **不支持函数和 `Symbol`**；

- 使用第三方库 (Lodash)：

  ```js
  npm install lodash
  
  import { cloneDeep } from 'lodash';
  
  const newObj = cloneDeep(obj);
  ```



检查对象自身属性（非原型链上属性）的方法：

| 方法                                       | 类型         | 作用           | 检查原型链？ | 安全性 | 返回值             |
| ------------------------------------------ | ------------ | -------------- | ------------ | ------ | ------------------ |
| **obj.hasOwnProperty()**                   | **实例方法** | 判断单个属性   | 否           | 低     | 布尔值             |
| **Object.prototype.hasOwnProperty.call()** | 静态方法     | 判断单个属性   | 否           | 高     | 布尔值             |
| **🌟Object.hasOwn()**                       | 静态方法     | 判断单个属性   | 否           | 高     | 布尔值             |
| **Reflect.ownKeys()**                      | 静态方法     | 获取所有属性键 | 否           | 高     | 数组（包含所有键） |

-  `hasOwnProperty`是实例方法，如果该实例本身有`hasOwnProperty`方法，会出现覆盖不准确的问题；

  ```js
  const obj = { a: 1, hasOwnProperty: () => false }; 
  
  console.log(obj.hasOwnProperty("a")); // false，执行的是obj上hasOwnProperty方法
  console.log(Object.prototype.hasOwnProperty.call(obj, "a")); // true
  console.log(Object.hasOwn(obj, "a")); // true
  console.log(Reflect.ownKeys(obj)); // [ 'a', 'hasOwnProperty' ]
  ```

  

Map/WeakMap、Set/WeakSet：

- Map/Set是**强引用，可遍历，可存储任何类型**；
- WeakMap/WeakSet是弱引用，不可遍历，**只能存储对象类型（WeakMap键只能是对象类型，WeakSet存储值只能是对象类型）**；
- **强引用**：**只要一个对象被引用着，垃圾回收机制就不会回收它**；即只要Map实例引用某个对象obj，即便将该对象设为 `null`，这个键对象和对应的值也不会被垃圾回收
- **弱引用**：是指一个对象如果只有弱引用指向它，那么垃圾回收机制可以回收这个对象；
- 为什么使用WeakMap而不是Map：深拷贝中，缓存表只是一个临时的辅助工具，用来处理循环引用。当拷贝完成后，这个缓存表就没有存在的必要；
- **可遍历**：`Set/Map` 有 `size` 属性，并且可以使用 `for...of` 循环、`forEach` 或 `keys()`, `values()`, `entries()` 方法进行遍历；

## 3. ✅promise.all

P86-89

```js
function promiseAll(promises){
  return new Promise((resolve, reject) => {
    if(!Array.isArray(promises)) return reject(new TypeError('promises要求是数组'));
    
    const res = [];
    let pendingCount = promises.length;
    
    if(pendingCount === 0) return resolve([]);
    
    promises.forEach((item, index) => {
      Promise.resolve(item)
      	.then(value => {
        	res[index] = value;
        	pendingCount--;
        	if(pendingCount === 0) return resolve(res);
      	})
      	.catch(reject);
    })
  })
}

const p1 = Promise.resolve('111');
const p2 = Promise.resolve('222');
const p3 = Promise.reject('!!!');

promiseAll([p1, p2, p3])
	.then(results => {
  	console.log('全部成功：', results);
	})
	.catch(error => {
  	console.log('至少一个失败：', error);
	})
```

## 4. ✅new

P79 

`new` 关键字：调用构造函数并返回一个对象；

```js
function Person(name) {
  this.name = name;
}

const p = new Person('zhangsan');
```



new执行过程：

- 创建一个空对象，将空对象的原型指向构造函数的prototype：`p.__proto__ = Person.prototype`
- 执行构造函数，把 `this` 绑定到新对象上：`Person.call(p, 'zhangsan')`

```js
function myNew(Constructor, ...args){
  const obj = Object.create(Constructor.prototype);
  const res = Constructor.apply(obj, args); // 或者call(obj, ...args)
  return (typeof res !== null && typeof res === 'object') ? res : obj; // 原始数据类型不能挂原型链，直接返回
}
```

## 5. ✅instanceof

P80

```js
function mtInstanceof(left, right){
  if(left === null || typeof left !== 'object') return false;
  
  let _proto = Object.getPrototypeOf(left);
  let _prototype = right.prototype;
  
  while(_proto !== null){
    if(_proto === _prototype) return true;
    _proto = Object.getPrototypeOf(_proto);
  }
  return false;
}

function Person(name){
  this.name;
}
const p = new Person('zhangsan');
console.log(p instanceof Person);
console.log(p instanceof Array);
```

- `instanceof` 是用来判断 **某个对象**是否是 **某个构造函数构造的实例**，而原始数据类型（ `string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`）**不是对象**，没有原型链，**无法参与原型查找**，所以使用 `instanceof` 判断返回 `false`；

- `Object.getPrototypeOf(obj)`等价于`obj.__proto__`（非标准写法）：

  ```js
  Object.getPrototypeOf(person) === Person.prototype;
  Object.getPrototypeOf(Person.protorype) === Object.prototype;
  Object.getPrototypeOf(Object.prototype) === null;
  ```

## 6. ✅flat（数组扁平化）

P81

数组扁平化：把 `[1, [2, [3, 4]], 5]` 扁平 1 层得到 `[1, 2, [3, 4], 5]`；扁平无限层（完全扁平）得到 `[1,2,3,4,5]`；

```js
// 数组扁平化
function myFlat(arr, depth){
  return depth > 0
  	? arr.reduce((acc, cur) => {
    		return acc.concat(Array.isArray(cur) ? myFlat(cur, depth - 1) : cur);
  	}, [])
  	: arr.slice(); // 数组的浅拷贝，不改变原数组；slice()不指定参数是浅拷贝；也可[...arr]实现浅拷贝
}

console.log(myFlat([1,[2,[3]]], 2));

// 对象扁平化
function objectFlat(obj = {}) {
  const res = {}
  function flat(item, preKey = '') {
    Object.entries(item).forEach(([key, val]) => {
      const newKey = preKey ? `${preKey}.${key}` : key
      if (val && typeof val === 'object') {
        flat(val, newKey)
      } else {
        res[newKey] = val
      }
    })
  }
  flat(obj)
  return res
}	
```

## 7. ✅call

P82

call：**让一个对象obj临时“拥有”某个函数func，并立即调用这个函数**，同时还能传入参数

```js
Function.prototype.myCall = function(context, ...args){
  if(typeof this !== 'function') throw new Typerror(this + 'is not a function');
  context = (context === undefined || context === null) ? globalThis : Object(context);
  
  const fnKey = Symbol('fn'); // Symbol生成唯一值，防止键名冲突
  context[fnKey] = this; // context.fn = this可能会覆盖context中原有fn方法
  const res = context[fnKey](...args);
  delete context[fnKey];
  
  return res;
}

function myName(city){
  return `my name is ${this.name}, from ${city}`;
}
const person = {name : 'zhangsan'};
console.log(myName.myCall(person, 'Beijing'));
```

- `globalThis` 是 **ES2020** 引入的全局对象统一名字；
  - 浏览器中全局为window、nodejs环境中为global、Web Worker中为self；

## 8. ✅函数柯⾥化

P82-83

柯里化：把一个接受多个参数的函数，转换成一系列每次只接受一个参数的函数；即让函数“分步接收参数”，而不是一次性接收所有参数；

```js
function curry(fn){
  return function curried(...args){
    if(args.length >= fn.length){
      return fn.apply(this, args);
    }else{
      return (...rest) => curried.apply(this, [...args, ...rest]);
    }
  }
}

function add(a, b, c){
  console.log(a + b + c);
}
const addCurry = curry(add);
addCurry(1)(2)(3);
```

- **fn.length函数定义时**声明的形参个数，不包括rest参数和默认值的参数；
- args.length表示已收集到的参数数量；
- rest为后传递进来的参数，[...args, ...rest]表示将先后传递进来的参数合并；

## 9. ✅数组api

P89

```js
Array.prototype.myMap = function(fn){
  const res = [];
  for(let i = 0;i < this.length;i++){ // this指向Array
    res.push(fn(this[i], i, this));
  }
  return res;
}

Array.prototype.myFilter = function(fn){
  const res = [];
  for(let i = 0;i < this.length;i++){
    if(fn(this[i], i, this)) res.push(this[i]); // 注意pushthis[i]而不是fn(this[i], i, this)，因为fn返回值为true/false
  }
  return res;
}

Array.prototype.myReduce = function(fn, initValue){ // 或者使用(fn, ...args)
  let res, start = 0;
  if(arguments !== 1){ // 提供了初始值
    res = initValue;
  }else{
    res = this[0];
    start = 1;
  }
  for(let i = start;i < this.length;i++){
    res = fn(res, this[i], i, this);
  }
  return res;
}

const arr1 = [1,2,3].myMap(item => item * 2);
const arr2 = [1,2,3].myFilter(item => item % 2 === 0);
const arr3 = [1,2,3].myReduce((acc, cur) => acc + cur, 0);
console.log(arr1, arr2, arr3);
```

- 将自定义数组方法挂载到 `Array.prototype` 上，所有数组都可以直接使用该自定义方法；
- map遍历数组，对每个数组元素执行fn，然后返回新数组；
- filter遍历数组，对每个数组元素执行fn，符合条件留下，然后返回新数组；
- reduce遍历数组，对每个数组元素执行fn，结果累加到第二个参数，全部遍历结束返回累加结果；

## 10. ✅千分位隔开

P83-84 p91-92

```js
function thousandSeparator(n){
  let s = n.toString();
  
  let sign = ''; // 处理负数
  if(s[0] === '-'){
    sign = '-';
    s = s.slice(1); // s由 -123 -> 123
  }
  
  let [leftPart, rightPart] = s.split('.'); // 拆分整数和小数
  
  const res = [];
  let count = 0;
  for(let i = leftPart.length - 1;i >= 0;i--){ // 加入'.'分隔符
    res.push(s[i]);
    count++;
    if(count % 3 === 0 && i !== 0) res.push('.');
  }
  
  const leftReverse = res.reverse().join('') // 加入分隔符后整数部分反转、['1','2'] -> '12'
  return sign + (rightPart ? `${leftReverse}.${rightPart}` : leftReverse);
}

console.log(thousandSeparator(-9876543.21));
```

## 11. ✅url解析

```js
function parseUrl(url){
  const queryIndex = url.indexOf('?');
  if(queryIndex === -1) return {}; // 无?的url直接返回空对象
  
  const rightUrl = url.slice(queryIndex + 1);
  const resObj = {};
  
  for(let str of rightUrl.split('&')){
    if(!str) continue;
    
    let [key, value] = str.split('=');
    key = decodeURIComponent(key);
    if(value === undefined){ // &间无等号：如&flag&；布尔简写参数；
      resObj[key] = true;
     	continue;
    }
    value = decodeURIComponent(value);
    
    if(resObj.hasOwnProperty(key)){
      resObj[key] = [resObj[key]].concat(value);
    }else if(value === 'undefined'){ // value值为undefined：如flag=undefined；
      resObj[key] = true;
    }else{
      resObj[key] = value; // 正常值 或 value为空：如a=
    }
  }
  return resObj;
}

console.log(parseUrl('http://example.com')); // 无?的url测试
console.log(parseUrl('http://example.com?flag')); // 无=测试
console.log(parseUrl('http://example.com?flag=undefined')); // value值为'undefiend'测试
console.log(parseUrl('http://example.com?flag=&name=zhangsan')); // value值为空或正常值测试
```

## 12. ✅数组转树 暴⼒递归和哈希解法

P70 

```js
function toTree(list, parId){
  let len = list.length;
  function func(parId){
    let res = [];
    for(let i = 0;i < len;i++){
      let item = list[i];
      if(item.pid === parId){
        item.children = func(item.id);
        res.push(item);
      }
    }
    return res;
  }
  return func(parId);
}

const list = [
  {id : 1, pid : 0, name : 'A'}, 
  {id : 2, pid : 1, name : 'B'}, 
  {id : 3, pid : 1, name : 'C'},
  {id : 4, pid : 2, name : 'D'}
];
console.log(JSON.stringify(toTree(list, 0), null, 2));
```

- `JSON.stringify(value, replacer, space)`：直接打印，对象太深，会折叠成[Object]；而JSON.stringify 会**把整个对象完整展开**；
  - value：要展开的对象；
  - replacer：对象过滤操作，null表示不过滤；
  - space：缩进几个空格；

## 13. ✅消息订阅发布

```js
// 简单版
class EventEmitter{
  constructor(){
    // {}可继承原型上方法和属性，若{}内部有以toString为属性名的方法，可能与原型上toString方法冲突
    this.events = {};
  }
  // 订阅
  on(name, fn){
    // // 一个事件名name（比如 "click"、"data"）可以对应多个回调函数，因此使用[]保存
    if(!this.events[name]) this.events[name] = [];
    this.events[name].push(fn);
  }
  // 取消订阅
  off(name, fn){
    if(!this.events[name]) return; // // 不相等保留；相等丢弃
    this.events[name] = this.events[name].filter(item => item !== fn); 
  }
  // 发布
  emit(name, ...args){
    if(this.events[name]) this.events[name].forEach(fn => fn(...args));
  }
  // 一次订阅
  once(name, fn){
    const wrapper = (...args) => {
      fn(...args);
      this.off(name, wrapper);
    }
    this.on(name, wrapper);
  }
}

// 改进版：Map存储键值对避免原型上方法干扰、
class EventEmitter {
  constructor() {
    this.events = new Map(); // 用 Map 存储事件和回调
  }

  on(name, fn) {
    if (!this.events.has(name)) {
      this.events.set(name, []);
    }
    const handlers = this.events.get(name);
    if (!handlers.includes(fn)) {
      handlers.push(fn);
    }
  }

  off(name, fn) {
    if (!this.events.has(name)) return;
    const handlers = this.events.get(name);
    const idx = handlers.indexOf(fn);
    if (idx > -1) { // splice(start, deleteCount)，返回修改后的新数组；
      handlers.splice(idx, 1);
    }
    if (handlers.length === 0) { // 键name中值为空，删除键节省空间
      this.events.delete(name);
    }
  }

  emit(name, ...args) {
    if (!this.events.has(name)) return;
    const handlers = [...this.events.get(name)]; // 拷贝一份，避免迭代过程中修改
    for (const fn of handlers) {
      fn.apply(this, args);
    }
  }

  once(name, fn) {
    const wrapper = (...args) => {
      fn.apply(this, args);
      this.off(name, wrapper);
    };
    this.on(name, wrapper);
  }
}
```




