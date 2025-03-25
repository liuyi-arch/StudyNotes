# **JavaScript 模块化编程**
模块化编程的核心思想是**将代码拆分为独立的模块**，每个模块负责特定功能，避免全局变量污染，提高代码的**可维护性、可复用性**。

---

## **1. 为什么需要模块化？**
在没有模块化之前，JavaScript 代码通常以**全局变量和函数**的方式组织，这会导致：
- **命名冲突**：不同文件可能使用相同变量，导致覆盖。
- **代码难以维护**：所有逻辑写在一个大文件中，难以管理。
- **依赖管理混乱**：无法清晰地知道某个文件依赖哪些代码。

---

## **2. JavaScript 模块化发展**
| 方式 | 特点 | 适用场景 |
|------|------|--------|
| 立即执行函数（IIFE） | 解决全局变量污染问题 | 早期 JS 代码组织方式 |
| CommonJS | `require()` 同步加载，适用于后端（Node.js） | Node.js |
| AMD（RequireJS） | `define()` 异步加载，适用于前端 | 旧版前端（浏览器） |
| UMD | 兼容 CommonJS 和 AMD | 需要兼容多种环境 |
| **ES Module（ESM）** | `import/export`，浏览器和 Node.js 都支持 | 现代前端、后端 |

---

## **3. 立即执行函数（IIFE）**
在 ES6 之前，常用 IIFE（Immediately Invoked Function Expression）实现模块化：
```js
var myModule = (function () {
    var privateVar = "I am private"; // 私有变量
    function privateFunc() {
        console.log("This is private");
    }
    
    return {
        publicFunc: function () {
            console.log("This is public");
            privateFunc();
        }
    };
})();

myModule.publicFunc(); // ✅ "This is public" + "This is private"
console.log(myModule.privateVar); // ❌ undefined
```
> **特点**：
> - **封装私有变量**，避免全局污染。
> - 只能手动管理依赖，代码耦合度较高。

---

## **4. CommonJS（用于 Node.js）**
CommonJS 是 Node.js 采用的模块化标准，使用 `require` 导入，`module.exports` 导出。

### **4.1 导出模块**
```js
// math.js
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;
module.exports = { add, subtract };
```
### **4.2 导入模块**
```js
// main.js
const math = require("./math");
console.log(math.add(2, 3)); // ✅ 5
```
> **特点**：
> - **同步加载**，适用于服务器端。
> - **不能在浏览器端直接使用**，需要 Webpack 或 Babel 处理。

---

## **5. AMD（RequireJS，适用于前端）**
AMD 通过 `define` 定义模块，`require` 加载模块（**异步加载**）。
### **5.1 定义模块**
```js
// math.js
define([], function () {
    return {
        add: function (a, b) {
            return a + b;
        }
    };
});
```
### **5.2 加载模块**
```js
// main.js
require(["math"], function (math) {
    console.log(math.add(2, 3)); // ✅ 5
});
```
> **特点**：
> - **异步加载**，提高性能。
> - 适用于**浏览器端**，但现在被 ES Module 取代。

---

## **6. ES Module（ESM）—— 现代前端模块化**
ES6 引入了 `import/export`，让前端模块化变得更清晰和高效。

### **6.1 导出模块**
```js
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
```
### **6.2 导入模块**
```js
// main.js
import { add } from "./math.js";
console.log(add(2, 3)); // ✅ 5
```
> **特点**：
> - **支持异步加载**
> - **浏览器和 Node.js 都支持**
> - **静态分析**，能更好地进行 Tree Shaking（删除无用代码）

### **6.3 默认导出（Default Export）**
```js
// message.js
export default function message() {
    console.log("Hello, ES Modules!");
}
```
```js
// main.js
import message from "./message.js";
message(); // ✅ "Hello, ES Modules!"
```
> `export default` 适用于**一个模块只导出一个功能**。

---

## **7. ES Module 在浏览器中的使用**
现代浏览器支持 ES 模块，可以直接在 HTML 中使用：
```html
<script type="module">
    import { add } from "./math.js";
    console.log(add(2, 3)); // ✅ 5
</script>
```
> **注意**：
> - **必须在服务器环境运行**，否则浏览器可能会报 CORS 错误。
> - **默认是延迟执行（defer）**，不会阻塞 HTML 解析。

---

## **8. ES Module 在 Node.js 中使用**
Node.js 14+ 版本已经支持 ES Module，需要：
1. **修改 `package.json`**
```json
{
    "type": "module"
}
```
2. **使用 `import/export`**
```js
// math.js
export const add = (a, b) => a + b;
```
```js
// main.js
import { add } from "./math.js";
console.log(add(2, 3));
```
> **也可以使用 `.mjs` 作为文件后缀**（`math.mjs`）。

---

## **9. Webpack + ES Module 进行前端模块化**
Webpack 可以打包 ES Module 代码，使其兼容低版本浏览器：
```js
// src/math.js
export const add = (a, b) => a + b;
```
```js
// src/main.js
import { add } from "./math.js";
console.log(add(5, 10));
```
```js
// webpack.config.js
module.exports = {
    entry: "./src/main.js",
    output: {
        filename: "bundle.js"
    },
    mode: "development"
};
```
> **优点**：
> - **打包多个模块**，减少 HTTP 请求。
> - **支持 Tree Shaking**，减少无用代码。

---

在企业级开发中，模块化不仅仅是拆分代码，还需要考虑**团队协作、依赖管理、性能优化和代码复用**。以下是一些关键的模块化开发技巧，帮助你在企业项目中更高效地组织代码。

---

## **11. 采用合适的模块化方案**
### **前端**
- **ES Module（ESM）**：适用于现代前端开发，`import/export` 方式管理代码。
- **Webpack / Vite / Rollup**：打包工具，优化模块加载，提高性能。
- **组件化开发**（React/Vue）：
  - 业务模块拆分为 UI 组件（例如按钮、表单、弹窗等）。
  - 使用 `npm package` 维护共享组件库。

### **后端**
- **CommonJS（CJS）**：Node.js 传统模块化方案，使用 `require()`。
- **ES Module（ESM）**：Node.js 14+ 支持，未来趋势。
- **微服务架构**：将后端拆分为多个独立服务，如 `用户管理`、`订单管理`，避免单体应用变得过于庞大。

---

## **12. 代码拆分和复用**
### **12.1 目录结构（前端示例）**
```plaintext
src/
│── components/       # 复用 UI 组件
│   ├── Button.js
│   ├── Modal.js
│── modules/          # 业务逻辑模块
│   ├── auth.js       # 认证模块
│   ├── user.js       # 用户管理
│── utils/            # 工具函数
│   ├── http.js       # 封装 axios 请求
│   ├── storage.js    # 本地存储封装
│── views/            # 页面
│   ├── Home.js
│   ├── Profile.js
│── index.js          # 入口文件
```
> **分层管理代码，避免所有逻辑都堆在 `index.js` 里。**

### **12.2 复用公共工具**
封装 **API 请求**
```js
// utils/http.js
import axios from "axios";

const request = axios.create({
    baseURL: "https://api.example.com",
    timeout: 5000
});

// 拦截请求，添加 Token
request.interceptors.request.use(config => {
    config.headers["Authorization"] = `Bearer ${localStorage.getItem("token")}`;
    return config;
});

export default request;
```
> **每个模块都能直接 `import request` 进行 API 请求，避免重复代码。**

---

## **13. 依赖管理**
### **13.1 使用 npm/yarn 统一依赖**
在企业团队协作中，**不同开发者的环境要保持一致**，推荐：
```bash
# 使用 npm 还是 yarn，团队需要统一
npm install   # 安装依赖
npm ci        # 安装 package-lock.json 确保版本一致
```
> **使用 `package-lock.json` 或 `yarn.lock` 确保团队成员安装的依赖一致，避免“我这能跑，你那报错”问题。**

### **13.2 Monorepo vs. Multirepo**
- **Monorepo（单一代码仓库）**：多个模块放在一个 Git 仓库，比如使用 `pnpm workspace` 统一管理依赖。
- **Multirepo（多仓库管理）**：每个模块独立存储，适用于微服务架构。

**Monorepo 适合大型前端项目，Multirepo 适合后端微服务。**

---

## **14. 模块通信**
### **14.1 事件总线（Event Bus）**
如果模块间需要通信（但不想直接调用对方的函数），可以使用**事件总线**：
```js
// eventBus.js
class EventBus {
    constructor() {
        this.events = {};
    }

    on(event, callback) {
        if (!this.events[event]) this.events[event] = [];
        this.events[event].push(callback);
    }

    emit(event, data) {
        if (this.events[event]) {
            this.events[event].forEach(callback => callback(data));
        }
    }
}

export default new EventBus();
```
> **避免模块间的直接耦合，提高可维护性。**

---

## **15. 代码拆分（Code Splitting）**
在**大型前端项目**中，**按需加载模块**可以显著提升性能：
```js
import(/* webpackChunkName: "lodash" */ "lodash").then(({ default: _ }) => {
    console.log(_.chunk([1, 2, 3, 4], 2)); // ✅ [ [1, 2], [3, 4] ]
});
```
> Webpack 会自动把 `lodash` 单独打包，提高首屏加载速度。

---

## **16. 组件库和 NPM 私有包**
### **16.1 搭建企业级组件库**
如果企业内多个项目需要用到相同的 UI 组件库，可以搭建自己的 NPM 私有库：
```bash
npm init
npm publish --access=restricted  # 发布到私有 npm
```
> **团队成员可以直接 `npm install @company/ui-library` 进行复用，避免重复造轮子。**

### **16.2 共享业务逻辑**
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

---

## **17. 微前端架构（适用于大型前端项目）**
当多个团队负责不同的功能模块时，可以使用**微前端（Micro Frontend）**：
- **独立部署**不同子应用，如 `用户管理`、`订单管理` 分别由不同团队维护。
- **使用 `iframe` 或 `module federation`** 进行模块拼接。

示例：
```js
// Webpack Module Federation
new ModuleFederationPlugin({
    name: "app1",
    filename: "remoteEntry.js",
    exposes: {
        "./Button": "./src/Button"
    }
});
```
> **微前端适用于大型项目，避免代码耦合过高，提高协作效率。**

---

## **18. 代码规范 & Lint 规则**
团队开发需要统一**代码风格**：
- **ESLint** + **Prettier**：保证代码风格一致。
- **Husky + Lint-staged**：在 Git 提交前自动检查代码质量：
  ```bash
  npx husky install
  npx husky add .husky/pre-commit "npx lint-staged"
  ```

---

## **总结**
| 技巧 | 关键点 |
|------|------|
| **模块化管理** | 采用 ESM / CommonJS / Webpack 进行拆分 |
| **代码组织** | 按组件、业务模块、工具函数分类 |
| **依赖管理** | 统一使用 npm/yarn，使用 Monorepo 或 Multirepo |
| **模块通信** | 事件总线（EventBus），避免耦合 |
| **按需加载** | Webpack Code Splitting 提升性能 |
| **共享模块** | 组件库 & 私有 npm 包，提高复用性 |
| **微前端架构** | 适用于大型前端团队协作 |
| **代码规范** | ESLint + Prettier + Husky 统一代码风格 |

---

## **总结**
| 模块化方式 | 适用环境 | 加载方式 |
|------------|--------|--------|
| IIFE | 早期 JS | 立即执行 |
| CommonJS | Node.js | `require()`（同步） |
| AMD | 旧版前端 | `define()`（异步） |
| UMD | 兼容 CommonJS + AMD | 兼容两者 |
| **ES Module（ESM）** | 现代前端 & Node.js | `import/export`（异步） |

### **最佳实践**
- **前端开发**：使用 **ES Module** (`import/export`)。
- **Node.js 开发**：使用 **ES Module 或 CommonJS**（ESM 是未来趋势）。
- **打包工具**：Webpack 结合 ESM 进行优化。

> - HTML中使用ES模块需在服务器环境中运行，否则浏览器出现CORS错误，服务器环境？
> - Webpack 可以打包 ES模块示例代码理解？
> - 模块化目录结构前端示例；
> - 使用npm/yarn统一依赖和版本（npm ci）安装package-lock.json确保版本一致？
> - **Monorepo（单一代码仓库）**：多个模块放在一个 Git 仓库，比如使用 `pnpm workspace` 统一管理依赖？
> - **Multirepo（多仓库管理）**：每个模块独立存储，适用于微服务架构？
> - 使用事件总线实现模块间通信，示例代码？
