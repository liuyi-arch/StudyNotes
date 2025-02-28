[参考MND文档及文档效果演示](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)

position取值：
- static：默认值，元素根据文档的常规流程进行定位，此时 top, right, bottom, left 和 z-index 属性无效；
- relative：相对于原本位置进行偏移，原本所占的空间不会改变（即留下空白），偏移不会影响其他元素的位置；
- absolute：元素会被移出正常文档流，并不为元素预留空间，相对于最近的非static定位祖先元素偏移；
- fixed：元素会被移出正常文档流，并不为元素预留空间，相对于屏幕视口的位置来指定元素位置；
- sticky：元素根据正常文档流进行定位，相对它的最近滚动祖先定位；
