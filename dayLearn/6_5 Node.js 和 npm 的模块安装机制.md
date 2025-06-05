在 account-book-react 根目录 和 account-book-react/src 目录执行npm命令，比如：
```shell
npm install zarm -S
```
结果是否相同？

### 1. 若只有account-book-react 根目录存在package.json，那么结果相同
理由是：npm 会从当前目录向上查找最近的 package.json。如果在 src/ 下执行，而 src/ 没有 package.json，它会往上找到 account-book-react/package.json 并在那里安装。

### 2. 如果 src/ 下也有 package.json，安装的依赖只对 src/ 生效，和根项目隔离
这种情况叫做多包（monorepo-like）结构。
