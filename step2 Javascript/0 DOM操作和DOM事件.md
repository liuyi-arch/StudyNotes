DOM是 JavaScript 操作 **HTML** 和 **XML 文档**的 API；

**BOM 包含 window、navigator、location、history、screen 对象，而 DOM 是 window 中的一个属性：window.document**；

## 1. DOM操作
### 1.1 DOM选择元素
通过**Id、ClassName、TagName、querySelector() 和 querySelectorAll()选择元素**；

```javascript
let element = document.getElementById("myId");
let elements = document.getElementsByClassName("myClass");
let elements = document.getElementsByTagName("p");

let element = document.querySelector(".myClass"); // 选择**第一个**符合条件的元素
let elements = document.querySelectorAll(".myClass"); // 选择**所有**符合条件的元素
console.log(elements); // NodeList（可以使用 `forEach` 遍历）
```

### 1.2 DOM修改HTML内容、属性、样式
通过**innerHTML、textContent**修改HTML内容；

通过**setAttribute('属性名', '属性值')设置属性、getAttribute('属性名')获取属性、removeAttribute('属性名')删除属性**，注意：这里修改的是**HTML中的属性，即写在 HTML 标签里**的属性；

通过**style样式**修改，这里修改的是**JavaScript 中 DOM 元素对象的属性，即document.xx.xx获取的属性**；

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
###  1.3 创建、添加、删除元素
通过**createElement()、appendChild()、removeChild()创建、插入、删除元素**；

```javascript
let newDiv = document.createElement("div"); // 创建 `div` 元素

// `appendChild()` 插入到父元素的最后
container.appendChild(newDiv);

element.remove(); // 直接删除（现代浏览器支持）
```

## 2. DOM事件
### 2.1 事件监听
**onclick 或 addEventListener 监听事件，addEventListener可以绑定多个事件，移除某个事件removeEventListener**；

注意：removeEventListener 必须传入同一个函数引用，**匿名函数无法解绑**；

```javascript
let btn = document.getElementById("myButton");

// 方法 1：使用 `onclick`
btn.onclick = function () { console.log('clicked A') };
btn.onclick = function () { console.log('clicked B') }; // 会覆盖上面那个


// 方法 2：使用 `addEventListener`
btn.addEventListener('click', () => console.log('clicked A'));
btn.addEventListener('click', () => console.log('clicked B'));

function handleClick() {
  console.log('Clicked!');
}
btn.addEventListener('click', handleClick);
btn.removeEventListener('click', handleClick); // 精准解绑
```

### 2.2 事件对象event
**事件处理函数的第一个参数 event（或 e）包含事件相关信息**；

```javascript
document.getElementById("btn").addEventListener("click", function(event) {
    console.log(event.target); // 触发事件的元素
    console.log(event.type); // 事件类型
    console.log(event.clientX, event.clientY); // 鼠标点击位置
});
```

event.preventDefault()阻止默认行为、event.stopPropagation()阻止事件冒泡到父元素；

### 2.3 事件委托
将**事件绑定在父元素**上，由父元素处理子元素的事件，提高性能；

```javascript
document.getElementById("list").addEventListener("click", function(event) {
    if (event.target.tagName === "LI") { // 确保点击的是 `<li>`
        console.log("点击了:", event.target.textContent);
    }
});
```

## 3. BOM浏览器对象模型
用的比较少，略；

