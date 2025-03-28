# var、let、const 变量提升

| 类型 | 是否提升？ | 初始化默认值？	| 能否在声明前使用？ |
| ---- | --- | --- | --- |
| var 变量 | 是	| undefined	| 可以，但值是 undefined |
| let 变量 | 是（存在暂存性死区） | 不初始化 |	ReferenceError |
| const 变量 | 是（存在暂存性死区） | 不初始化 | ReferenceError |

### 暂时性死区

如果 const x = 2 声明完全没有提升，那么 console.log(x) 语句应该能够读取上层作用域的 x 值。事实上，console.log(x) 语句读取的是 const x = 2 声明的 x，抛出ReferenceError错误。花括号内const x = 2以上区域称为变量x的暂时性死区。

```js
const x = 1;
{
  console.log(x); // ReferenceError
  const x = 2;
}
```

总结：

1. 尽量使用 let 或 const 代替 var，避免 undefined 访问问题；
2. 将变量声明放在使用之前，增强代码可读性；

以上是var、let、const 提升，还有其他诸如function 提升、class 提升、import 提升。

参考：https://developer.mozilla.org/zh-CN/docs/Glossary/Hoisting
