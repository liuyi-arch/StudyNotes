# HTML

## 1. src与herf的区别？

主要是”用途“和“加载行为”的区别：

- src常用于`<script>、<img>`，会将资源下载到页面中，会**阻塞 HTML 渲染**；
- href常用于 `<link>` 引入样式或 `<a>` 超链接，是引用资源，不会阻塞 HTML 渲染；

## 2. ⻚⾯导⼊样式时，使⽤ link 和 @import 有什么区别？

主要是归属区别、加载区别和兼容性区别：

- `<link>` 是 HTML 标签，页面解析时并行加载，不存在兼容性问题；
- `@import`是CSS语法，页面解析完后加载，是CSS2.1才有的语法，不适用于低版本IE浏览器；

## 3. 🌟对HTML语义化的理解 ？语义化标签？

主要是增强代码可读性，常见语义化标签有`<header>、<footer>、<main>、<nav>、<title>、<p>`；

## 4. script标签中defer和async的区别？

- srcipt默认是同步加载，顺序执行，会阻塞HTML渲染；
- `<script defer>`是异步加载，先执行完HTML解析再执行JS脚本；
- `async` 是异步加载，谁先加载完谁先执行；

​	如下图，蓝⾊代表js脚本⽹络加载时间，红⾊代表js脚本执⾏时间，绿⾊代表html解析：	![截屏2025-07-30 10.08.20](/Users/liuliu/Desktop/截屏2025-07-30 10.08.20.png)

## 5. meta标签？

用于设置页面基础配置，比较熟悉的有：

- `<meta charset="UTF-8">`用于设置编码，防止乱码；
- `<meta name="viewport">`移动端适配；

## 6. 🌟HTML5有哪些更新？

- 新的选择器`document.querySelector`、`document.querySelectorAll`；
- 音媒体`<audio>`、`<video>`标签，此前`flash`实现；
- 本地存储：`localStorage`、`sessionStorage`；
- 语义化标签；
- 浏览器通知`Notifications`、地理位置`Geolocation`；
- 扩展表单组件：新增`<input>`类型，如`data`、`range`、`email`；
- `WebSocket`支持全双工通信；
- 多任务处理`web worker`；

## 7. 对Web Worker的理解？

- Web Worker 是一种浏览器的多线程技术，允许在后台线程中运行 JavaScript 代码，避免阻塞主线程；
- 但是Worker 不能直接访问 DOM、`window` 或 `document`，只能通过消息传递来交换数据（使用 `postMessage` 和 `onmessage`）；

## 8. `<!DOCTYPE>`作用是什么？标准模式与兼容模式各有什么区别？

- 告诉浏览器以标准规范解析网页，若DOCTYPE 不存在则会以兼容模式呈现；
- 标准模式的渲染⽅式和 JS 引擎的解析⽅式都是以该浏览器⽀持的最⾼标准运⾏；
- 兼容模式中，⻚⾯以宽松的向后兼容的⽅式显示；

## 9. ⾏内元素有哪些？块级元素有哪些？ 空(void)元素有那些？

- 行内元素：`span input img button label`等；
- 块级元素：`div p title h1 ul ol li`等；
- 空元素即没有闭合标签元素：`<input> <img> <link> <meta>`等；

## 10. img标签title、alt、srcset?

- alt：图⽚加载失败时，显示alt的内容，利于SEO（搜索引擎更好地理解图片内容）；

- title：⿏标移动到图⽚上时，显示title的内容；

- srcset：不同设备或屏幕尺寸下加载的不同图像，提升响应式设计和图片加载性能；

  ```html
  <img src="small.jpg" srcset="large.jpg 1024w, medium.jpg 640w" alt="响应式图片">
  ```

## 11. iframe?

- `iframe` 是在当前页面中嵌入另一个 HTML 页面，可以用来嵌入广告、第三方组件、跨域页面、文档预览等；
- 但 `iframe` 存在一些性能和安全问题，比如阻塞父页面加载、跨域限制、SEO 不友好；

```html
<iframe src="https://example.com" width="600" height="400"></iframe>
```

## 12. title与h1的区别、b与strong的区别、i与em的区别？

- **`<title>`** 是网页标题，鼠标hover至浏览器标签页显示标题；**`<h1>`** 是网页内容标题，用于页面主体结构，且有明确层级；
- **`<b>`** 是加粗但没有语义；**`<strong>`** 加粗并表示“强调”；
- **`<i>`** 是斜体没有语义；**`<em>`** 表示“强调”，默认也是斜体，能被语音设备识别；

## 13. head 标签有什么作⽤

- `<head>` 是 HTML 页面中非可视化部分的容器，用来引⽤脚本、指示样式表、放置页面元信息；
- `<title>`网页标题、`<meta>`编码方式、`<link>`引入外部CSS文件、`<style>`页面样式、`<script>`JS脚本；
- 其中`<title>`是head部分唯一必需的元素；

## 14. 布局模型

- Normal Flow流动模型：标准文档流布局；
- Float浮动模型：常用于图文混排，脱离文档流，需清楚浮动；
- Position层模型：可实现悬浮按钮；
