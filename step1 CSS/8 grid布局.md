# 参考文档
- [css-tricks](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-grid-area)
- [Rachel 文章](https://www.smashingmagazine.com/native-css-masonry-layout-css-grid/)
- [Firefox规范文档](https://drafts.csswg.org/css-grid-3/#masonry-layout)

# 网格容器属性（父元素）
### 1. display：grid | inline-grid；即生成块级网格容器还是行内网格容器；
### 2. grid-template-columns和grid-template-rows：line-name track-size；指定行名和间隔大小；
```css
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```

<img width="186" alt="image" src="https://github.com/user-attachments/assets/258b43d2-65f1-435d-9f91-88c85589bc4a" />


**repeat()**：
```css
.container {
  grid-template-columns: 1fr 2fr 1fr 2fr 1fr 2fr;
  grid-template-rows: 100px auto 20% 100px auto 20px;
}
/*等价于*/
.container {
  grid-template-columns: repeat(3, 1fr 2fr);
  grid-template-rows: repeat(2, 100px auto 20%);
}


.container {
  grid-template-columns: repeat(4, [col] 1fr);
}
/*等价于*/
.container {
  grid-template-columns:
    [col] 1fr
    [col] 1fr
    [col] 1fr
    [col] 1fr;
}


/*重复符号末尾的命名网格线最终与下一个起始名称线共享同一行*/
.container {
  grid-template-rows: repeat(5, [row-start] 150px [row-end]);
}
/*等价于*/
.container {
  grid-template-rows:
    [row-start] 150px
    [row-end row-start] 150px
    [row-end row-start] 150px
    [row-end row-start] 150px
    [row-end row-start] 150px [row-end];
}
```

**fr**：即所占比例；如果两列的宽度分别为1fr和2fr，就表示后者是前者的两倍；

**minmax使用**： `grid-template-rows:minmax(10px, 1fr) 3fr`；

**auto关键词**：表示由浏览器自己决定长度；

注意：一行可以有多个名称；
```css
.container {
  grid-template-rows: [row1-start] 25% [row1-end row2-start] 25% [row2-end];
}
```

### 3. grid-template-areas：grid-area-name | . | none；grid-area-name指定网格区域的名称、. 表示空网格单元、none表示没有定义网格区域；
```css
/*关于grid-area为项目指定名称，以便使用该 grid-template-areas 属性创建的模板可以引用它*/
.item-a {
  grid-area: header;
}
.item-b {
  grid-area: main;
}
.item-c {
  grid-area: sidebar;
}
.item-d {
  grid-area: footer;
}

.container {
  display: grid;
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: auto;
  grid-template-areas: 
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```

<img width="188" alt="image" src="https://github.com/user-attachments/assets/a2218d30-f7aa-446c-919a-298edf0b3d62" />

注意：此语法仅命名区域，不能使用此语法命名线条；
### 4. grid-column-gap、grid-row-gap（最新更改为column-gap、row-gap）和grid-gap（最新更改为gap）；指定列间隙和行间隙；
```css
.container {
  column-gap: 10px;
  row-gap: 15px;
}
```

<img width="185" alt="image" src="https://github.com/user-attachments/assets/7d86e5fc-6b10-4335-8efe-6245d96317e7" />

注意：仅对列/行之间 ，而不是外边缘；

grid-gap（最新更改为gap）：<row-gap> <column-gap>；column-gap和row-gap的简写形式；

### 5. justify-items、align-items和place-items；网格单元格中块沿行/纵轴对齐；
- justify-items: start | end | center | stretch；
例如，justify-items: start；

<img width="188" alt="image" src="https://github.com/user-attachments/assets/7714a9de-fd48-4531-90f4-fd60fbf1a534" />

- align-items: start | end | center | stretch | baseline（沿文本基准线对齐）；
例如，align-items: start；

<img width="190" alt="image" src="https://github.com/user-attachments/assets/cb68c65b-0c59-4c9b-9333-2f66aaa7d3ca" />

- place-items：align-items和justify-items的简写；第一个值设置align-items，第二个值设置justify-items，如果只设置一个值，则将第一个值分配给两个属性；

### 6. justify-content、align-content和place-content；整个网格在容器中对齐方式，注意与上述第5条属性区别；
- justify-content: start | end | center | stretch | space-around | space-between | space-evenly；
依次表示如下效果：

<img width="382" alt="image" src="https://github.com/user-attachments/assets/91697770-d6fb-49a9-a8bd-11298d1b5cde" />

- align-content: start | end | center | stretch | space-around | space-between | space-evenly；与justify-content为横向竖向的关系；
- place-content：第一个值设置align-content，第二个值设置justify-content，如果省略第二个值，则将第一个值分配给两个属性；

### 7. grid-auto-columns和grid-auto-rows；当网格项多于网格中的单元格，将自动生成指定大小网格；
例如，grid-auto-columns: 60px；

### 8. grid-auto-flow: row | column | row dense | column dense；若有未明确放置在网格项中的块，将在空余网格中按行/列依次放置；
四个属性效果依次为：先行后列、先列后行、后续两个属性不作解释（css-tricks没有作详细说明）；

<img width="125" alt="image" src="https://github.com/user-attachments/assets/bba5bc0b-4cd2-4a34-bf9b-78bee55b0adc" />

### 9. grid-template和grid：
- grid-template属性是grid-template-columns、grid-template-rows和grid-template-areas这三个属性的合并简写形式；
- grid属性是grid-template-rows、grid-template-columns、grid-template-areas、 grid-auto-rows、grid-auto-columns、grid-auto-flow这六个属性的合并简写形式；

# 网格项属性（子元素）
### 1. grid-column-start、grid-column-end、grid-row-start和grid-row-end；定义网格块在网格中位置；

```css
.item {
  grid-column-start: <number> | <name> | span <number> | span <name> | auto;
}
```

依次表示指定网格线从左至右/从上至下第几条、网格线名称、跨越几个网格、（等同网格线名称？文档未描述清楚）、自动；

示例：

```css
.item-a {
  grid-column-start: 2;
  grid-column-end: five;
  grid-row-start: row1-start;
  grid-row-end: 3;
}
```

<img width="203" alt="image" src="https://github.com/user-attachments/assets/f6051086-c8bb-4d4b-a623-11a8aad2e9e8" />

```css
.item-b {
  grid-column-start: 1;
  grid-column-end: span col4-start;
  grid-row-start: 2;
  grid-row-end: span 2;
}
```

<img width="202" alt="image" src="https://github.com/user-attachments/assets/65de68d4-0f61-4cd4-aba8-e1a9b29ac5a3" />

注意：如果没有声明grid-column-end/grid-row-end，则该项目将默认跨越1个网格；项目可以使用z-index实现相互重叠；

### 2. grid-column和grid-row；grid-column是grid-column-start和grid-column-end的合并简写形式；grid-row同理；

```css
.item {
  grid-column: <start-line> / <end-line> | <start-line> / span <value>;
}
```

注意：指定开头，结尾和跨几个网格有一个即可；

```css
.item-c {
  grid-column: 3 / span 2;
  grid-row: third-line / 4;
}
```

<img width="200" alt="image" src="https://github.com/user-attachments/assets/1bfe05a1-db7c-4cca-baac-246b7e5f1dbf" />

### 3. grid-area：为项目指定名称；

```css
.item {
  grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end>;
}
```

比如，为下面这个块指定名称为header；

```css
.item-d {
  grid-area: header;
}

.item-d {
  grid-area: 1 / col4-start / last-line / 6;
}
```

在网格容器属性中第三条grid-template-areas中可以看到如何用自定义的块名使用块；

### 4. justify-self、align-self和place-self：定义块在单元格中的放置方式；

```css
.item {
  justify-self: start | end | center | stretch;
}

各属性效果依次如下：

<img width="248" alt="image" src="https://github.com/user-attachments/assets/ed1fea9b-2f91-4892-8ddd-51edea8aaeb1" />

align-self同理，与justify-self行/列的区别；

place-self是align-self和justify-self的合并简写形式；第一个值设置align-self，第二个值设置justify-self，如果省略第二个值，则将第一个值分配给两个属性；





