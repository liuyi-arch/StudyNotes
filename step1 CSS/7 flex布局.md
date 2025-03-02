# 参考文档
- [CSS-tricks](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [阮一峰flex布局教程](https://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)

# 弹性容器属性（父元素）
1. flex-direction：默认值为row；
```css
.container {
  flex-direction: row | row-reverse | column | column-reverse;
}
```
<img width="187" alt="image" src="https://github.com/user-attachments/assets/93c31193-85e3-41a5-8810-0cf26bad2dc4" />

2. flex-wrap：默认值为nowrap；
```css
.container {
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```
<img width="184" alt="image" src="https://github.com/user-attachments/assets/587a59dd-9de8-4f7c-94ed-ffda8e0c458b" />

3. flex-flow：是flex-direction和flex-wrap的简写形式，默认值为row nowrap；

4. justify-content：默认值为flex-start；
```css
.container {
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly | start | end | left | right ... + safe | unsafe;
}
```
<img width="186" alt="image" src="https://github.com/user-attachments/assets/f209fed5-ede7-4cd1-93a7-c76b0981d601" />


5. align-items：默认值为stretch，如果项目未设置高度或设为auto，将占满整个容器的高度（即仍然尊重最小宽度/最大宽度）
```css
.container {
  align-items: stretch | flex-start | flex-end | center | baseline | first baseline | last baseline | start | end | self-start | self-end + ... safe | unsafe;
}
```
<img width="187" alt="image" src="https://github.com/user-attachments/assets/e1026755-ea5a-487b-8913-a5038fd5b8e9" />

6. align-content：默认值为normal，仅对多行弹性容器有效；
```css
.container {
  align-content: flex-start | flex-end | center | space-between | space-around | space-evenly | stretch | start | end | baseline | first baseline | last baseline + ... safe | unsafe;
}
```
<img width="188" alt="image" src="https://github.com/user-attachments/assets/c4026083-7d1a-4862-9e06-5b418cb10e2b" />


7. gap：它仅将间距应用于不在外边缘的项目之间；
<img width="185" alt="image" src="https://github.com/user-attachments/assets/5b8eb61f-e63b-4ca6-ba3e-6717c6b0a07e" />

# 弹性项目属性（子元素）
1. order：定义元素的排列顺序，数值越小，排列越靠前，默认为0；
```html
<div class="flexBox">
    <div style="order: 3;">j</div>
    <div style="order: 1;">k</div>
    <div style="order: 2;">l</div>
</div>
     
<style type="text/css">
    .flexBox{
        display: flex;
    }
</style>
```
<img width="185" alt="image" src="https://github.com/user-attachments/assets/ffc49adc-4a78-44ae-90cd-49016132548e" />

可以设置为负数；

2. flex-grow：定义子元素**占父元素剩余空间宽度的比例**，即一个父元素宽度为800px，三个子元素一行布局，设置width样式定义其宽度依次为100px、100px、200px，则剩余空间400px；若三个子元素flex-grow值依次为1、2、2，则三个子元素依次分得剩余空间为80px、160px、160px，因此三个子元素最终宽度依次为180px、260px、360px；

注意：负数无效；

3. flex-shrink：定义子元素**超出父元素空间宽度收缩的比例**，与flex-grow相反；负数无效；

4. flex-basis: 20%、5rem | auto；定义元素在分配剩余空间**之前**的默认大小；

5. flex：是flex-grow, flex-shrink和flex-basis的简写，默认值0 1 auto；

6. align-self：允许单个成员有与其他成员不一样的对齐方式，可覆盖align-items属性；

