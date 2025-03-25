## **组件库和 NPM 私有包**
### **搭建企业级组件库**
如果企业内多个项目需要用到相同的 UI 组件库，可以搭建自己的 NPM 私有库：
```bash
npm init
npm publish --access=restricted  # 发布到私有 npm
```
> **团队成员可以直接 `npm install @company/ui-library` 进行复用，避免重复造轮子。**

### **共享业务逻辑**
如果多个项目需要共享相同的业务逻辑（如用户登录、权限管理），可以**封装成 npm 包**：
```js
// auth.js
export function login(username, password) {
    return request.post("/login", { username, password });
}
```
然后多个项目都可以这样使用：
```js
import { login } from "@company/auth-module";
```
