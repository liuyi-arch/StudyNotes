参考博客/文档：
- [(图文详细)最通俗易懂的CSS 浮动float属性详解](https://blog.csdn.net/qq_36595013/article/details/81810219)
- gpt搜索：`子元素设置浮动可能会造成父元素垂直方向高度坍塌，有哪些解决父元素高度坍塌的措施？`

为什么浮动会导致父元素高度坍塌：当元素浮动后（float: left; 或 float: right;），它会脱离正常文档流（漂浮在标准流之上），导致其父元素无法计算浮动子元素的高度；

浮动造成父元素坍塌解决：
- 方法一：（扩展性不好）给父元素直接设置高度，强制撑起浮动元素引起的坍塌区域；
- 方法二：（增加无意义标签，违反了结构与样式分离的原则）给需要清除浮动的元素后面添加空标签，空标签内添加`clear：both`样式，clear: both让元素的顶部不能与前面任何左浮动（left）或右浮动（right）的元素重叠；
- 方法三：（父元素的内容有滚动或溢出时，出现移除内容被隐藏）给父元素设置 `overflow: hidden`样式， 触发父元素创建一个**块级格式化上下文BFC**（Block Formatting Context），BFC会计算父元素的高度，以确保浮动子元素被包含在内；
- 方法四：给父元素添加一个伪元素来解决浮动问题，；
```css
.parent {
  overflow: hidden; /* 或使用clearfix */
}

.parent::after {
  content: "";
  display: block;
  clear: both;
}
```
- 方法五：给父元素使用CSS3新属性`display: flow-root`，设置后可以使父元素清除浮动，并自动包含浮动子元素；
- 方法六：给父元素设置 flex 或 grid 布局；
```css
.parent {
  display: flex;
}

.parent {
  display: grid;
}
```
