# **JavaScript DOM 操作与事件**
DOM（Document Object Model，文档对象模型）是 JavaScript 操作 HTML 和 XML 文档的 API。掌握 DOM 操作与事件，是前端开发的基础。

---

## **1. DOM 选择元素**
在操作 DOM 之前，首先要获取 HTML 页面中的元素。

### **1.1 通过 `id` 选择元素**
```js
let element = document.getElementById("myId");
console.log(element);
```
> 适用于 **唯一** 元素选择。

### **1.2 通过 `class` 选择元素**
```js
let elements = document.getElementsByClassName("myClass");
console.log(elements); // HTMLCollection（类数组）
```
> 适用于**多个相同类名的元素**，返回的是 **HTMLCollection**，不能直接使用数组方法。

### **1.3 通过 `tagName` 选择元素**
```js
let elements = document.getElementsByTagName("p");
console.log(elements);
```
> 获取所有 `<p>` 标签，返回 **HTMLCollection**。

### **1.4 通过 `querySelector()` 和 `querySelectorAll()`**
```js
let element = document.querySelector(".myClass"); // 选择**第一个**符合条件的元素
let elements = document.querySelectorAll(".myClass"); // 选择**所有**符合条件的元素
console.log(elements); // NodeList（可以使用 `forEach` 遍历）
```
> `querySelectorAll()` 返回的是 **NodeList**，可使用 `forEach()` 遍历。

---

## **2. DOM 操作**
### **2.1 修改 HTML 内容**
```js
let div = document.getElementById("myDiv");
div.innerHTML = "<strong>新内容</strong>"; // 支持 HTML 解析
div.textContent = "纯文本内容"; // 只设置文本，不解析 HTML
```

### **2.2 修改属性**
```js
let img = document.getElementById("myImage");
img.src = "new-image.jpg"; // 修改图片路径
img.setAttribute("alt", "新图片描述"); // 设置 `alt` 属性
console.log(img.getAttribute("alt")); // 获取 `alt` 属性
img.removeAttribute("alt"); // 删除 `alt` 属性
```

### **2.3 修改样式**
```js
let div = document.getElementById("myDiv");
div.style.color = "red"; // 修改字体颜色
div.style.fontSize = "20px"; // 修改字体大小
div.style.backgroundColor = "yellow"; // 修改背景色
```
> 但如果样式较多，建议使用 **`classList`** 操作类名：

```js
div.classList.add("active"); // 添加类
div.classList.remove("active"); // 移除类
div.classList.toggle("active"); // 切换类（有则移除，无则添加）
console.log(div.classList.contains("active")); // 判断是否有某个类
```

---

## **3. 创建、添加和删除元素**
### **3.1 创建新元素**
```js
let newDiv = document.createElement("div"); // 创建 `div` 元素
newDiv.textContent = "我是新元素"; // 设置内容
newDiv.classList.add("new-class"); // 添加类名
```

### **3.2 插入元素**
```js
let container = document.getElementById("container");

// `appendChild()` 插入到父元素的最后
container.appendChild(newDiv);

// `insertBefore()` 插入到指定元素前
let firstChild = container.firstElementChild;
container.insertBefore(newDiv, firstChild);
```

### **3.3 删除元素**
```js
let element = document.getElementById("myDiv");
element.remove(); // 直接删除（现代浏览器支持）

// `removeChild()`（兼容旧版浏览器）
element.parentNode.removeChild(element);
```

---

## **4. DOM 事件**
### **4.1 事件监听**
可以使用 `onclick` 或 `addEventListener` 监听事件：
```js
let btn = document.getElementById("myButton");

// 方法 1：使用 `onclick`
btn.onclick = function() {
    alert("按钮被点击！");
};

// 方法 2：使用 `addEventListener`
btn.addEventListener("click", function() {
    alert("按钮被点击！");
});
```
> `addEventListener` **推荐使用**，因为它可以绑定多个事件，不会覆盖之前的事件处理函数。

### **4.2 常见事件**
| 事件类型 | 说明 |
|---------|------|
| `click` | 单击事件 |
| `dblclick` | 双击事件 |
| `mouseenter` / `mouseleave` | 鼠标进入 / 离开 |
| `mouseover` / `mouseout` | 鼠标悬停 / 移出 |
| `keydown` / `keyup` | 键盘按下 / 松开 |
| `input` / `change` | 输入框输入 / 值改变 |
| `submit` | 表单提交 |
| `focus` / `blur` | 输入框获得 / 失去焦点 |

---

## **5. 事件对象 `event`**
事件处理函数的第一个参数 `event`（或 `e`）包含事件相关信息：
```js
document.getElementById("btn").addEventListener("click", function(event) {
    console.log(event.target); // 触发事件的元素
    console.log(event.type); // 事件类型
    console.log(event.clientX, event.clientY); // 鼠标点击位置
});
```

### **5.1 阻止默认行为**
```js
document.getElementById("myLink").addEventListener("click", function(event) {
    event.preventDefault(); // 阻止 `<a>` 链接跳转
});
```

### **5.2 阻止事件冒泡**
```js
document.getElementById("child").addEventListener("click", function(event) {
    event.stopPropagation(); // 阻止事件冒泡到父元素
});
```

---

## **6. 事件委托**
**事件委托（Event Delegation）** 是将事件绑定在父元素上，由父元素处理子元素的事件，提高性能：
```js
document.getElementById("list").addEventListener("click", function(event) {
    if (event.target.tagName === "LI") { // 确保点击的是 `<li>`
        console.log("点击了:", event.target.textContent);
    }
});
```
> 适用于**动态创建的子元素**，避免给每个子元素单独绑定事件。
>
> **因为子元素也属于父元素，所以事件绑定在父元素，点击子元素也能触发事件**；
>
> **通过event.target.tagName判断点击的是哪个子元素**；

---

> ## **总结**
> - 通过Id、ClassName、TagName、querySelector() 和 querySelectorAll()选择元素；
> - 通过innerHTML、textContent修改HTML内容；通过标签自带属性修改、通过style样式修改；
> - 通过createElement()、appendChild()、removeChild()创建、插入、删除元素；
> - onclick 或 addEventListener 监听事件，addEventListener可以绑定多个事件；
> - 事件处理函数的第一个参数 event（或 e）包含事件相关信息；
> - event.preventDefault()阻止默认行为、event.stopPropagation()阻止事件冒泡到父元素；
> - 事件委托（Event Delegation） ：将事件绑定在父元素上，由父元素处理子元素的事件，代码实现示例？
> - BOM由下列对象组成：
>     1. window：全局对象，是BOM的顶级对象；
>     2. navigator：获取浏览器信息
>     3. location：操作 URL
>     4. history：管理浏览记录
>     5. screen：屏幕相关信息
>     6. document（属于 DOM）
