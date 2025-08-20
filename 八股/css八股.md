## 1. flex布局

容器上属性：

- flex-direction、flex-wrap、justify-content、align-items、align-content；
- 设置主轴（默认左右）方向、是否换行、沿主轴（默认左右）对齐方式、沿交叉轴（默认上下）对齐方式、交叉轴多行对齐方式；



项目上属性：

- flex-grow、flex-shrink、flex-basis、align-self；
- 占剩余空间的比例、空间不足时缩小比例、项目初始大小、单个项目交叉轴对齐方式；



特点：可根据容器大小自动收缩，无需计算margin；适用于一维布局，比如导航栏，二维采用grid布局；

图示参考css-tricks：https://css-tricks.com/snippets/css/a-guide-to-flexbox/

## 2. 两栏布局三栏布局

- 两栏布局：左边定宽，右边自适应；

  - Flex：容器`display:flex`，左边设置宽度，右边`flex:1`自适应；
  - position+margin：左边绝对定位，设置宽度，右边`margin-left:200px`；
  - Float：左边左浮动，设置宽度，右边`margin-left:200px`；

- 三栏布局：左右两边固定宽度，中间自适应；

  ```css
  // 两栏布局：flex实现
  .container {
    display: flex;
  }
  .left {
    width: 200px;
  }
  .right {
    flex: 1; /* 自适应 */
  }
  
  // 两栏布局：position+margin
  .left {
    position: absolute;
    width: 200px;
    left: 0;
  }
  .right {
    margin-left: 200px;
  }
  
  // 两栏布局：float+margin
  .left {
    float: left;
    width: 200px;
  }
  .right {
    margin-left: 200px; /* 右侧自动填充 */
  }
  ```

  ```css
  // 三栏布局：flex实现
  .container {
    display: flex;
  }
  .left {
    width: 200px;
  }
  .middle {
    flex: 1; /* 自适应 */
  }
  .right {
    width: 200px;
  }
  
  // 三栏布局：float+margin
  .left {
    float: left;
    width: 200px;
  }
  .right {
    float: right;
    width: 200px;
  }
  .middle {
    margin: 0 200px;
  }
  ```

## 3. bfc

- BFC（Block Formatting Context，块级格式化上下文）：BFC内元素布局不会影响到外部元素；
- 常见触发条件：float` 值不是 `none、position` 值是 `absolute` 或 `fixed、display: inline-block；
- 解决浮动元素导致父元素高度塌陷、外边距（margin）重叠：`overflow: hidden`；

## 4. 居中

- 水平居中：

  - 行内元素：`text-align: center`；
  - 块级元素：`margin: 0 auto`；

- 垂直居中：

  ```css
  // 方式一：line-height值等于height值
  line-height: 100px
  
  // 方式二：absolute + transform
  .parent { position: relative; }
  .child {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  
  // flex
  .parent {
    display: flex;
    justify-content: center; /* 水平居中 */
    align-items: center;     /* 垂直居中 */
  }
  ```

## 5. 响应式布局

响应式与自适应：

- 响应式：一个页面，自动伸缩；能够自适应不同设备屏幕大小；

- 自适应：多个固定宽度版本，根据设备选择其一；



em、rem、vw、vh：

- em：基于当前元素font-size计算；1em = 当前元素的 font-size；层层父元素，还可能继承父元素font-size，计算复杂；
- rem：基于html根元素font-size计算；1rem = html 的 font-size；方便全局缩放，改根font-size，全局尺寸同步变；
- vw\vh：1vw = 视口宽度的 1%；



其他：

- 控制上下限防止字体太大太小：`html { font-size: clamp(14px, calc(100vw / 7.5), 20px); }`
- 满屏宽高使用vw/vh；
- postcss-pxtorem：px转rem；postcss-px-to-viewport：px转vw；

## 6. display

- display：block | inline | inline-block | flex | grid | none

## 7. position

- position：static ｜ relative ｜ absolute ｜ fixed ｜ sticky

## 8. css3新特性

- **选择器**：属性选择器 `[attr=value]`、伪类 `:nth-child()` 等。
- **过渡与动画**：`transition`、`@keyframes` + `animation`。
- **2D/3D 变换**：`transform: translate / rotate / scale / skew`。
- **弹性布局**：`flex`、`grid`。
- **媒体查询**：`@media` 响应式布局。
- **阴影**：`box-shadow`、`text-shadow`。
- **渐变**：`linear-gradient`、`radial-gradient`。
- **边框与背景**：`border-radius`、`background-size: cover`

## 9. 伪类与伪元素区别

- **伪类（Pseudo-class）**：用于选择元素的**状态**；`:hover` 悬停、`:focus` 聚焦、`:nth-child(n)` 选择第 n 个子元素；
- **伪元素（Pseudo-element）**：用于创建一些**不存在于 DOM 的元素**；`::before` 在元素内容前插入、`::after` 在元素内容后插入；

## 10. 画三角形

- 元素本身宽高为 0，用边框制造出三角形：

  ```css
  .triangle {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-bottom: 100px solid red;
  }
  ```

# 
