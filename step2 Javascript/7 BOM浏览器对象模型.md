# **JavaScript BOM（浏览器对象模型）**
BOM（Browser Object Model，浏览器对象模型）提供了与浏览器窗口交互的方法，允许开发者操作 **浏览器窗口、导航、历史记录、屏幕信息、定时器等**。

---

## **1. BOM 组成结构**
BOM 主要由以下对象组成：
- **`window`**：全局对象，包含所有 BOM 相关对象
- **`navigator`**：获取浏览器信息
- **`location`**：操作 URL
- **`history`**：管理浏览记录
- **`screen`**：屏幕相关信息
- **`document`**（属于 DOM）

---

## **2. `window` 对象**
`window` 是 **BOM 的顶级对象**，所有全局变量、函数、对象都是它的属性。

```js
console.log(window.innerWidth, window.innerHeight); // 窗口宽高
```

### **2.1 `window` 常见属性**
| 属性 | 作用 |
|------|------|
| `window.innerWidth` / `window.innerHeight` | 视口宽度 / 高度 |
| `window.outerWidth` / `window.outerHeight` | 浏览器窗口宽度 / 高度 |
| `window.scrollX` / `window.scrollY` | 页面滚动距离 |
| `window.screenX` / `window.screenY` | 浏览器相对于屏幕的位置 |
| `window.location` | 获取/操作 URL |
| `window.history` | 浏览器历史记录 |

### **2.2 `window` 方法**
#### **（1）弹窗相关**
```js
alert("Hello, BOM!"); // 弹出警告框
confirm("你确定要退出吗？"); // 确认框（返回 `true`/`false`）
prompt("请输入你的姓名："); // 输入框
```

#### **（2）定时器**
- **`setTimeout()`**：延时执行（只执行一次）
- **`setInterval()`**：定时执行（循环执行）
- **`clearTimeout()` / `clearInterval()`**：清除定时器

```js
let timer = setTimeout(() => console.log("3 秒后执行"), 3000);
clearTimeout(timer); // 取消定时器

let interval = setInterval(() => console.log("每 2 秒执行一次"), 2000);
clearInterval(interval); // 取消循环执行
```

#### **（3）打开/关闭窗口**
```js
let newWin = window.open("https://www.google.com", "_blank", "width=500,height=500"); // 打开新窗口
newWin.close(); // 关闭新窗口
```

---

## **3. `navigator` 对象**
`navigator` 对象提供浏览器相关信息。

### **3.1 常见属性**
```js
console.log(navigator.userAgent); // 浏览器的 UA 信息
console.log(navigator.language); // 用户的语言
console.log(navigator.platform); // 操作系统类型
console.log(navigator.onLine); // 用户是否在线
```

> **应用场景**：
> - 判断用户设备（移动端 / PC）
> - 判断浏览器类型

**示例：判断是否为移动端**
```js
const isMobile = /Mobi|Android|iPhone/i.test(navigator.userAgent);
console.log(isMobile ? "移动端设备" : "PC 端设备");
```

---

## **4. `location` 对象**
`location` 对象用于操作 **URL 地址**。

### **4.1 `location` 常见属性**
```js
console.log(location.href); // 当前 URL
console.log(location.protocol); // 协议（http/https）
console.log(location.host); // 主机名 + 端口号
console.log(location.hostname); // 主机名
console.log(location.port); // 端口号
console.log(location.pathname); // 路径
console.log(location.search); // 查询参数（?后面的部分）
console.log(location.hash); // URL 哈希（#后面的部分）
```

### **4.2 `location` 相关方法**
```js
location.assign("https://www.example.com"); // 跳转到新页面
location.replace("https://www.example.com"); // 替换当前页面（不能返回）
location.reload(); // 刷新当前页面
```

> **区别**：
> - `assign()` **会保留历史记录**（可以返回）
> - `replace()` **不会保留历史记录**（无法返回）

---

## **5. `history` 对象**
`history` 用于**操作浏览器的历史记录**。

### **5.1 `history` 相关方法**
```js
history.back(); // 返回上一页（等同于浏览器后退）
history.forward(); // 前进到下一页
history.go(-2); // 前进 / 后退指定的页面
console.log(history.length); // 历史记录数量
```
> **注意**：`history` 只能在**同源**页面中操作，不能跨域访问其他网站的历史记录。

---

## **6. `screen` 对象**
`screen` 提供**屏幕相关信息**。

### **6.1 `screen` 相关属性**
```js
console.log(screen.width, screen.height); // 屏幕分辨率
console.log(screen.availWidth, screen.availHeight); // 可用屏幕大小
console.log(screen.colorDepth); // 颜色深度
```

> **应用场景**：
> - 适配移动端 / PC 端屏幕
> - 根据屏幕分辨率提供不同的页面布局

---

## **7. 实战案例**
### **7.1 获取 URL 参数**
```js
function getQueryParams() {
    let params = {};
    let queryString = location.search.substring(1); // 获取 ? 后面的参数
    let pairs = queryString.split("&");
    pairs.forEach(pair => {
        let [key, value] = pair.split("=");
        params[decodeURIComponent(key)] = decodeURIComponent(value);
    });
    return params;
}
console.log(getQueryParams()); // { name: "Alice", age: "25" }
```

---

### **7.2 监听窗口大小变化**
```js
window.addEventListener("resize", () => {
    console.log("窗口大小改变:", window.innerWidth, window.innerHeight);
});
```

---

### **7.3 判断用户是否在线**
```js
window.addEventListener("online", () => console.log("用户在线"));
window.addEventListener("offline", () => console.log("用户离线"));
```

---

### **7.4 监听用户离开页面**
```js
window.addEventListener("beforeunload", (event) => {
    event.returnValue = "你确定要离开吗？";
});
```

---

## **总结**
| 对象 | 作用 |
|------|------|
| `window` | 全局对象，管理窗口、定时器、弹窗等 |
| `navigator` | 获取浏览器信息（`userAgent`、`platform`） |
| `location` | 操作 URL（`href`、`reload()`、`replace()`） |
| `history` | 管理历史记录（`back()`、`forward()`、`go(n)`） |
| `screen` | 获取屏幕信息（`width`、`height`） |

BOM 让 JavaScript 能与浏览器交互，使网页更加**动态**，结合 DOM 操作可以实现更丰富的用户体验！🚀
