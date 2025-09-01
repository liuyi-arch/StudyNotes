## 1. 防抖、节流✅8.11✅

- 防抖：**在事件被触发n秒后再执⾏回调函数。如果在这段时间内⼜触发了该事件，则重新计时**。⽐如，在输⼊框搜索时，⽤户连续输⼊字符，我们不希望每次输⼊都触发搜索请求，⽽是在⽤户停⽌输⼊⼀段时间后再触发搜索请求。

- 节流：在**每隔n秒后执⾏⼀次回调函数**。⽐如，当⽤户滚动⻚⾯时，我们不希望每次滚动都触发相应的操作，⽽是每隔⼀段时间才执⾏⼀次操作。
- 注意：防抖节流函数优化版本都是首次立即执行。

P74-78

```js
// 防抖函数：版本1
function debounce(fn , delay = 300){
  let timer = null;
  return function(...args){
    if(timer) clearTimeout(timer);
    timer = setTimeout(() => { fn.call(this , ...args) } , delay);
  }
}

// 防抖函数-版本2
function debounce(fn , delay = 300 , immediate = false){
  let timer = null;
  let invoked = false; // 第一次点击立即执行
  return function(...args){
    const context = this;
    if(timer) clearTimeout(timer);
    if(immediate && !invoked){
      fn.call(context , ...args);
      invoked = true;
    }else{
      timer = setTimeout(() => { 
      	fn.call(context , ...args);
      	invoked = false;
      } , delay);
    }
  }
}

window.onresize = debounce(function() {
  console.log('window onresize end');
}, 500)
```

```js
// 节流函数-版本1
function throttle(fn, interval) {
  var timer = null;
  var firstTime = true;
  var _self = fn;
  return function() {
    var that = this;
    var args = arguments;
    // 判断是否第一次执行
    if(firstTime) {
      _self.apply(that, args);
      return firstTime = false;
    }
    // 判断定时器是否执行完毕
    if(timer) {
      return false;
    }
    // 设置定时器
    timer = setTimeout(function() {
      clearTimeout(timer);
      timer = null;
      _self.apply(that,args);
    }, interval || 500)
  }
}
window.onresize = throttle(function() {
  console.log('window onresize');
}, 500)

// 节流函数-版本1
function throttle(fn , delay = 300){
  let last = 0;
  return function(...args){
    const now = Date.now();
    if(now - last >= delay){
      fn.apply(this , args);
      last = now;
    }
  }
}

// 节流函数-版本2

```

## 2. 深浅拷⻉✅8.3✅

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

// 深拷贝-base版
function deepCopy(obj , cache = new WeakMap()){ // 默认参数，WeakMap 是一种键名只能是对象的键值对集合，用于记录该对象是否已被复制，避免重复拷贝和循环引用；
  if(obj === null || typeof obj !== 'object') return obj; // 原始数据类型直接返回；
  if(obj instanceof Date) return new Date(obj); // 内建对象不能用{}或[]拷贝；
  if(obj instanceof RegExp) return new RegExp(obj);
  if(obj instanceof Error) return new Error(obj.message); // 只返回报错message，不返回报错stack（即控制台报错的at <anonymous>...）；
  if(typeof obj === 'function'){
    return function(...args){
      return obj.call(this , ...args); // 拷贝函数本体功能和this传递（即谁调用指向谁），而不拷贝函数运行时的内存结构，比如函数的闭包上下文；
    }
  }
  
  if(cache.has(obj)) return cache.get(obj); // 比如，如果有obj.seft = obj，而没有cache缓存判断是否已被复制，那么将循环引用；
  const newObj = Array.isArray(obj) ? [] : {};
  cache.set(obj , newObj);
  
  for(const key in obj){
    if(obj.prototype.hasOwnProperty.call(value , key)){ // obj.prototype.hasOwnProperty.call(value , key)判断一个对象自身是否有某个属性，而不是继承来的属性，obj.hasOwnProperty(key)写法不能区分后者；
      newObj[key] = deepCopy(obj[key] , cache);
    }
  }
  
  return newObj;
}


// 深拷贝-large版
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
    const result = new Map()
    cache.set(obj, result)
    obj.forEach((value, key) => {
      result.set(deepCopy(key, cache), deepCopy(value, cache))
    })
    return result
  }

  // 处理 Set
  if (obj instanceof Set) {
    const result = new Set()
    cache.set(obj, result)
    obj.forEach(value => {
      result.add(deepCopy(value, cache))
    })
    return result
  }

  // 普通对象/数组
  const result = Array.isArray(obj) ? [] : {}
  cache.set(obj, result)

  // 支持 Symbol 属性
  const keys = Reflect.ownKeys(obj)
  for (const key of keys) {
    result[key] = deepCopy(obj[key], cache)
  }

  return result
}
```

## 3. promise.all✅8.13✅

P86-89

```js
function promiseAll(promises){
  return new Promise((resolve, reject) => {
    if(!Array.isArray(promises)) return reject(new TypeError('Promise.all 输入需要是数组'));
    
    const res = [];
    let pendingCount = promises.length;
    
    if(pendingCount === 0) return resolve([]);
    
    promises.forEach((item, index) => {
      Promise.resolve(item)
      	.then(value => {
        	res[index] = value;
        	pendingCount--;
        	if(pendingCount === 0) resolve(res);
      })
      .catch(reject);
    })
  })
}

// promises示例
const promises = [
 Promise.resolve('First'),
 Promise.reject('Rejected'),
 Promise.resolve('Third')
];
```

## 4. new✅8.11✅

P79 

```js
function myNew(Constructor, ...args){
  const obj = Object.create(Constructor.prototype); // 创建一个Constructor类型的实例对象，并将obj.__proto__指向Constructor.prototype
  const result = Constructor.apply(obj, args); // 执行构造函数并将this绑定到obj
  return (result !== null && (typeof result === 'object' || typeof result === 'function')) ? result : obj; // 原始数据类型不能不能挂原型链，也不能用new创建原始数据类型的实例
}
 
// myNew和new创建实例对象示例
function Person(name, age){
  this.name = name;
  this.age = age;
}

let person1 = new Person('zhangsan', 20); // {name: 'zhangsan', age: 20}
let person2 = myNew(Person, 'lisi', 30); // {name: 'lisi', age: 30}
```

## 5. instanceof✅8.3✅

P80

- `instanceof`用于判断一个对象是否是某个构造函数构造的实例：`object instanceof Constructor`；

- 它的判断逻辑是：沿着对象的原型链向上查找，看是否有原型等于 `Constructor.prototype`；

```js
function myInstanceof(left , right){
  if(typeof left === null || typeof left !== 'Object') return false;
  let proto = Object.getPrototypeOf(left);
  let prototype = right.prototype;
  while(proto !== null){ // 沿着原型链查找，直到null；
    if(proto === prototype) return true;
    proto = Object.getPrototypeOf(proto);
  }
  return false;
}
```

- `instanceof` 是用来判断 **某个对象**是否是 **某个构造函数构造的实例**，而原始数据类型（ `string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`）**不是对象**，没有原型链，**无法参与原型查找**，所以使用 `instanceof` 判断返回 `false`；

- `Object.getPrototypeOf(obj)`等价于`obj.__proto__`（非标准写法）：

  ```js
  Object.getPrototypeOf(person) === Person.prototype;
  Object.getPrototypeOf(Person.protorype) === Object.prototype;
  Object.getPrototypeOf(Object.prototype) === null;
  ```

## 6. flat（数组扁平化）✅8.13✅

P81

- 扁平化就是把嵌套的数组变成“少一层或多层嵌套”的数组；

- 例如把 `[1, [2, [3, 4]], 5]` 扁平 1 层得到 `[1, 2, [3, 4], 5]`，扁平无限层（完全扁平）得到 `[1,2,3,4,5]`；

```js
// 数组扁平化
const flat = (arr, depth = 1){
  depth > 0
  	? arr.reduce((acc, cur) =>
    		arr.concat(Array.isArray(cur) ? flat(cur, depth - 1) : cur), [])
  	: arr.slice(); // 实现数组的浅拷贝，slice()不指定参数是浅拷贝，指定参数为截取，也可以使用[...arr]
}

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

## 7. call✅8.12✅

P82 

call：**让一个对象obj临时“拥有”某个函数func，并立即调用这个函数**，同时还能传入参数

```js
func.call(obj, ...args) // 如果obj为undefined或null，那么func的this指向为全局而不是obj
```

- 把 `func` 挂到 `obj` 上；

- 然后用obj调用func，传入实参为arg1、arg2等，这样 `this` 就指向了 `obj`；
- 调用完之后再把func方法从obj删掉；

```js
// call是Function原型上的方法，看原生call绑定代码帮助理解`func.call(obj , ...args)`
Function.prototype.myCall = function(thisArg , ...args){
	if(typeof this !== 'function'){
    throw new TypeError(this + 'is not a function');
  }
  
  // globalThis为不同运行环境中的全局对象，Object(thisArg)包装为Object类型
  thisArg = (thisArg === undefined || thisArg === null) 
    ? globalThis : Object(thisArg) 
  
  // 将func添加到obj，这里也就是thisArg上
  const fnKey = Symbol('fn'); // Symbol避免thisArg中属性冲突，这里的fn只是描述，供调试用
  thisArg[fnKey] = this; // .语法无法访问Symbol属性，必须使用[]，也就是for...in不能将Symbol类型枚举出来
  
  const res = thisArg[fnKey](...args); // 执行func，然后将func从thisArg上删除
  delete thisArg[fnKey];
  
  return res;
}
```

## 8. 函数柯⾥化✅8.12✅

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

// 调用示例
function add(a, b, c){
  return a + b + c;
}

const addCurry = curry(add);
addCurry(1)(2)(3);
```

- **fn.length函数定义时**声明的形参个数，不包括rest参数和默认值的参数；
- args.length表示已收集到的参数数量；
- rest为后传递进来的参数，[...args, ...rest]表示将先后传递进来的参数合并；

P82-83

## 9. 数组api

P89

```js
Array.prototype.map = function(fn) {
  const res = []
  for (let i = 0; i < this.length; i++) {
    // fn 接收三个参数：元素、索引、原数组
    res.push(fn(this[i], i, this))
  }
  return res
}

Array.prototype.filter = function(fn) {
  const res = []
  for (let i = 0; i < this.length; i++) {
    // fn 返回 true 时，保留元素
    if (fn(this[i], i, this)) {
      res.push(this[i])
    }
  }
  return res
}

Array.prototype.reduce = function(fn, initValue) {
  let res, start = 0
  if (arguments.length !== 1) {
    // 如果有提供初始值，就用它作为累加器初始值
    res = initValue
  } else {
    // 如果没提供初始值，默认取数组第一个元素
    res = this[0]
    start = 1 // 从第二个元素开始 reduce
  }
  for (let i = start; i < this.length; i++) {
    // fn 接收：累计结果、当前元素、索引、原数组
    res = fn(res, this[i], i, this)
  }
  return res
}
```

## 10. url解析、千分位隔开

P83-84 p91-92

```js
const thousandSeparator = function (n) {
  // 1. 转为字符串，方便逐字符处理
  const str = n.toString();
  // 2. 存放结果字符
  const res = [];
  let count = 0; // 已处理的数字个数
  // 3. 从右往左遍历
  for (let i = str.length - 1; i >= 0; i--) {
    res.push(str[i]); // 当前数字加入结果
    count++;
    // 4. 每 3 个数字加一个分隔符（注意避免最左边多余的分隔符）
    if (count % 3 === 0 && i !== 0) {
      res.push(".");
    }
  }
  // 5. 反转数组并拼接成字符串
  return res.reverse().join("");
};

const thousandSeparator = function (n) {
 //先转字符串
 n = n.toString();
 let count = 0;
 const arr = [];
 for (let i = n.length - 1; i >= 0; i--) {
 count++;
 if (count < 4) {
 arr.push(n[i]);
 } else {
 arr.push(...[".", n[i]]);
 count = 1;
 }
 }
 return arr.reverse().join("");
};
```

## 11. 数组转树 暴⼒递归和哈希解法

P70 

```js
function toTree(list, parId) {
  let len = list.length;       // 保存数组长度，避免循环里频繁计算
  function loop(parId) {       // 递归函数，用于生成以 parId 为父节点的子树
    let res = [];              // 当前父节点的子节点集合
    for (let i = 0; i < len; i++) {  // 遍历整个 list
      let item = list[i];
      if (item.pid === parId) {      // 如果当前元素的父 id 符合要求
        item.children = loop(item.id);  // 递归构建它的子节点
        res.push(item);              // 加入到当前父节点的子集合
      }
    }
    return res;  // 返回当前层级的所有子节点
  }
  return loop(parId);  // 从指定的 parId（根节点）开始构造树
}
```

## 12. 消息订阅发布

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


