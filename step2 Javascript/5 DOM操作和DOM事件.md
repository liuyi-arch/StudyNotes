DOM是 JavaScript 操作 HTML 和 XML 文档的 API；

# DOM操作
### DOM选择元素

通过**Id、ClassName、TagName、querySelector() 和 querySelectorAll()选择元素**；

```javascript
let element = document.getElementById("myId");
let elements = document.getElementsByClassName("myClass");
let elements = document.getElementsByTagName("p");

let element = document.querySelector(".myClass"); // 选择**第一个**符合条件的元素
let elements = document.querySelectorAll(".myClass"); // 选择**所有**符合条件的元素
console.log(elements); // NodeList（可以使用 `forEach` 遍历）
```

### DOM修改、创建、添加、删除元素

通过**innerHTML、textContent**修改HTML内容；

通过**标签自带属性**修改、通过**style样式**修改；

```javascript
let div = document.getElementById("myDiv");
div.innerHTML = "<strong>新内容</strong>"; // 支持 HTML 解析
div.textContent = "纯文本内容"; // 只设置文本，不解析 HTML

let img = document.getElementById("myImage");
img.src = "new-image.jpg"; // 修改图片路径
img.setAttribute("alt", "新图片描述"); // 设置 `alt` 属性
console.log(img.getAttribute("alt")); // 获取 `alt` 属性
img.removeAttribute("alt"); // 删除 `alt` 属性

let div = document.getElementById("myDiv");
div.style.color = "red"; // 修改字体颜色
div.style.fontSize = "20px"; // 修改字体大小
div.style.backgroundColor = "yellow"; // 修改背景色
```

通过createElement()、appendChild()、removeChild()创建、插入、删除元素；

```javascript
let newDiv = document.createElement("div"); // 创建 `div` 元素

// `appendChild()` 插入到父元素的最后
container.appendChild(newDiv);

element.remove(); // 直接删除（现代浏览器支持）
```

# DOM事件
### 事件监听
**onclick 或 addEventListener 监听事件，addEventListener可以绑定多个事件**；

```javascript
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

### 事件对象
**事件处理函数的第一个参数 event（或 e）包含事件相关信息**；

```javascript
document.getElementById("btn").addEventListener("click", function(event) {
    console.log(event.target); // 触发事件的元素
    console.log(event.type); // 事件类型
    console.log(event.clientX, event.clientY); // 鼠标点击位置
});
```

event.preventDefault()阻止默认行为、event.stopPropagation()阻止事件冒泡到父元素；

### 事件委托
将事件绑定在父元素上，由父元素处理子元素的事件，提高性能；

```javascript
document.getElementById("list").addEventListener("click", function(event) {
    if (event.target.tagName === "LI") { // 确保点击的是 `<li>`
        console.log("点击了:", event.target.textContent);
    }
});
```
