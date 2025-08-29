## 1. flex布局✅

面试题：

1. （腾讯）Flexbox 与非 Flexbox 区别？

2. （滴滴）flex垂直居中有实现方式？如何用flex实现？

3. （海康威视）flex基础知识，主轴，交叉轴？常用属性？

4. （Momenta）用 CSS 实现 Flex 布局？子盒子宽度缩小时自动换行排列（可用 flex-wrap: wrap 实现）？

5. （京东）flex布局的属性；flex:1？

6. （字节）CSS 中 flex: 1 是什么意思？分别代表哪些属性的值？

7. （b站）讲一讲flex？运用flex如何实现header、content、footer上中下布局？

   ---

容器上属性：

- **display: flex** | inline-flex
- **flex-direction（决定主轴是水平还是垂直）**: row | row-reverse | column | column-reverse
- **flex-wrap**: nowrap | wrap | wrap-reverse
- flex-flow: `<direction> <wrap>`（语法糖）
- **justify-content（主轴对齐）**：flex-start | flex-end | center | space-between | space-around | space-evenly
- **align-items（交叉轴对齐）**：stretch | flex-start | center | flex-end | baseline
- **align-content**（**多行时**行与行交叉轴对齐）：同上，但只有**换行**时才生效

项目上属性：

- `order`（排序）
- flex（语法糖）: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
  - **`flex: 1` → `1 1 0%`（均分剩余空间，允许收缩，初始0）**
  - **`flex-grow`（主轴放大）、`flex-shrink`（主轴收缩）、`flex-basis`（主轴初始尺寸）**
- `align-self`（覆盖单个子项在交叉轴上的对齐）

特点：可根据容器大小自动收缩，无需计算margin；适用于一维布局，比如导航栏，二维采用grid布局；

场景应用：

```css
/*水平垂直居中*/
.parent {
  display: flex;
  justify-content: center;  /* 主轴居中 */
  align-items: center; /* 交叉轴居中 */
  flex-wrap: wrap; /* 允许换行 */
  height: 200px;
}

/*固定左右 中间自适应*/
.layout {
  display: flex;
}
.left, .right { flex: 0 0 200px; }
.main { flex: 1; min-width: 0; } /* 防止被内容撑溢出 */

/*多行卡片瀑布式*/
.cards {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
}
.card { flex: 0 0 240px; }

/*header、content、footer上中下*/
.container{
  display: flex;
  flex-direction: column;
}
.header, .footer{
  height: 200px;
}
.content{
  flex: 1; /*等价于flex-grow: 1;flex-shrink: 1;flex-basis: 0%*/
}
```

图示参考css-tricks：https://css-tricks.com/snippets/css/a-guide-to-flexbox/

## 2. 两栏布局三栏布局✅

- 两栏布局：左边定宽，右边自适应；

  - Flex：容器`display:flex`，左边设置宽度，右边`flex:1`自适应；
  - position+margin：左边绝对定位，设置宽度，右边`margin-left:200px`；
  - Float：左边左浮动，设置宽度，右边`margin-left:200px`；

- 三栏布局：左右两边固定宽度，中间自适应；

  ```css
  /*两栏布局：flex实现*/
  .container {
    display: flex;
  }
  .left {
    width: 200px;
  }
  .right {
    flex: 1; /* 自适应 */
  }
  
  /*两栏布局：position+margin*/
  .left {
    position: absolute;
    width: 200px;
    left: 0;
  }
  .right {
    margin-left: 200px;
  }
  
  /*两栏布局：float+margin*/
  .left {
    float: left;
    width: 200px;
  }
  .right {
    margin-left: 200px; /* 右侧自动填充 */
  }
  ```

  ```css
  /*三栏布局：flex实现*/
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
  
  /*三栏布局：float+margin*/
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

面试题：（京东）bfc认识？

---

- BFC（Block Formatting Context，块级格式化上下文）：BFC内元素布局不会影响到外部元素；
- 常见触发条件：float` 值不是 `none、position` 值是 `absolute` 或 `fixed、display: inline-block；
- 解决浮动元素导致父元素高度塌陷、外边距（margin）重叠：`overflow: hidden`；

## 4. 居中✅

面试题：

- （滴滴）csS垂直居中哪些实现方式？

- （京东）垂直居中实现？

- （字节）元素水平、垂直居中分别有什么方法？

- （美团）行内元素与块级元素的区别及居中方案？

  ---

- 行内元素linline与块级元素block：

  - 块级元素独占一行、可设置宽高、margin上下左右均有效；
  - 行内元素一行多个、**不可设置宽高、margin上下无效**；
    - **img特殊，是行内元素，但可设置宽高**；
  - **inline-block：既像行内元素不独占一行，又像块级元素可以设置宽高、margin；**

- 水平垂直居中常见解决方案：**注意样式是加在child上还是parent上**

  ```css
  /*行内、块级元素水平居中*/
  /*1. 行内元素*/
  .parent{
    text-align: center;
  }
  /*2. 块级元素*/
  .child{
    margin: 0 auto;
    width: 200px; /*必须要有宽度*/
  }
  
  /*行内元素垂直居中*/
  .parent{
    height: 100px;
    line-height: 100px;
  }
  
  /*水平垂直居中*/
  /*1. flex实现*/
  .parent{
    display: flex;
    justify-content: center; /*水平*/
    align-items: center; /*垂直*/
  }
  /*2. gird实现*/
  .parent{
    display: grid;
    place-items: center; /*水平垂直居中*/
  }
  /*3. absolute + transform实现*/
  .child{
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%); /*相对自身大小向上向左偏移50%*/
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

面试题：

- （滴滴）position: absolute, relative和fixed？

- （海康威视）position所有属性？absolute是根据什么定位的？

  ---

- position：static ｜ relative ｜ absolute ｜ fixed ｜ sticky

  - absolute根据**最近的position 非 static**的祖先定位

  - **fixed常见场景悬浮按钮；sticky常见场景固定表头**；

    ```js
    .sticky-box {
      position: sticky;
      top: 0; /* 到达视口顶部时“吸附” */
    }
    ```

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
