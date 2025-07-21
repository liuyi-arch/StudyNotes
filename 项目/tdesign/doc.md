### **1. 初始化项目基本结构**

- 执行 `npm create vite` 搭建基本结构（Vue + TypeScript 模板）
- 安装基础依赖：Vue 3、Vite、TypeScript、TDesign

---

#### 1.1 创建 Vite 项目

```bash
npm create vite@latest . -- --template vue-ts
```

> 当前目录已有 `.github` 和其他文件， `.` 表示在当前目录初始化，而不是创建新目录。
>
> `npm init vite` 不是官方推荐语法，容易和旧 CLI 混淆，`npm create vite` 是标准化入口。

#### 1.2 安装项目基础依赖

安装 Vue 3、Vite、TypeScript（部分已在模板中，以下为确保完整性）：

```bash
npm install
```

#### 1.3 当前目录结构

```bash
.
├── README.md
├── index.html
├── package.json
├── public
│   └── vite.svg
├── src
│   ├── App.vue
│   ├── assets
│   ├── components
│   ├── main.ts
│   ├── style.css
│   └── vite-env.d.ts
├── tsconfig.app.json
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts
```

#### 1.4 启动项目进行验证

```bash
npm run dev
```

- 访问 [http://localhost:5173](http://localhost:5173/) 应该能看到 Vite 默认欢迎页。
- 此时说明 Vue3 + TS 项目结构已搭建完毕。

#### 1.5 一些问题及解决

##### 1.5.1 main.ts文件出现：`找不到模块“./App.vue”或其相应的类型声明。`

报错来自 TypeScript 无法识别 `.vue` 文件类型，因为 `vite-env.d.ts` 文件为空，`tsconfig.json` 也没有 `include` 指定 `vite-env.d.ts`。

1.  `src/vite-env.d.ts` 增加下列内容，添加对 `.vue` 文件的类型声明：

```ts
// 原有代码
/// <reference types="vite/client" />

declare module '*.vue' {
  import { DefineComponent } from 'vue'
  const component: DefineComponent<{}, {}, any>
  export default component
}
```

2. 当前 `tsconfig.app.json` 中的 `include` 缺少`.d.ts`，TypeScript 无法识别`.d.ts`文件。作如下修改：

```json
{
  // ... 
  "include": ["src/**/*.ts", "src/**/*.tsx", "src/**/*.vue"]
}
// 修改为：
{
  // ...
  "include": ["src/**/*.ts", "src/**/*.tsx", "src/**/*.vue", "src/**/*.d.ts"]
}
```

##### 1.5.2 main.ts文件中出现：找不到模块“./style.css”或其相应的类型声明。

`vite-env.d.ts`文件添加下列内容：

```ts
declare module '*.vue' {
    import { DefineComponent } from 'vue'
    const component: DefineComponent<{}, {}, any>
    export default component
}
// 告诉 TypeScript：“我允许导入 .css 文件”
declare module '*.css';
```

---

### 2. 安装工程化依赖

- 生产依赖：安装 UI 框架`tdesign-mobile-vue`
- 开发依赖：
  - `unplugin-auto-import`
  - `unplugin-vue-components`
  - `unplugin-vue-router`
  - `@antfu/eslint-config`
  - （建议）`@types/node`（开发时类型提示）、`vue-tsc`（类型检测）

---

#### 2.1 安装 UI 框架（生产依赖）

```bash
npm install tdesign-mobile-vue
```

#### 2.2 安装自动导入相关插件（开发依赖）

```bash
npm install -D unplugin-auto-import unplugin-vue-components unplugin-vue-router
```

- `unplugin-auto-import`: 直接使用 Vue API（如 `ref`、`computed`）、pinia等，无需每次手动 import
- `unplugin-vue-components`: 自动按需加载组件
- `unplugin-vue-router`: 自动根据目录结构生成路由配置

#### 2.3 安装 ESLint 配置与类型支持（开发依赖）

```bash
npm install -D @antfu/eslint-config
```

这是 antfu 的一体化 ESLint 风格配置，包含：

- 单引号 `'`
- 无分号
- 自动 fix
- Prettier 风格整合
- 支持 `<script setup>` + Vue3 + TypeScript

#### 2.4 安装类型支持（建议）

为确保 IDE 友好、ts 严格检查建议安装以下类型库（有些 vite 模板已内置）：

```bash
npm install -D @types/node
```

> 如果你打算后续添加 `axios`、`vue-router`、`pinia` 等，也需要配套添加类型支持。

#### 2.5 其他

##### 2.5.1 关于Composition API 的 `<script setup>` 语法糖

> 使用 `<script setup>`，需Vue 版本 ≥ 3.2。
>
> `npm list vue`打印版本。

1. 传统 Composition API：

```vue
<!-- MyButton.vue -->
<template>
  <button @click="increment">点击 {{ count }} 次</button>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

export default defineComponent({
  setup() {
    const count = ref(0)
    const increment = () => count.value++

    return {
      count,
      increment
    }
  }
})
</script>

<style scoped>
button {
  padding: 10px;
}
</style>
```

- 需使用 `defineComponent({ setup() { ... } })` 明确结构。

- 所有变量和方法都需要显式地 `return` 出来。

2. 使用 `<script setup>`（语法糖）：

```vue
<!-- MyButton.vue -->
<template>
  <button @click="count++">点击 {{ count }} 次</button>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)
</script>

<style scoped>
button {
  padding: 10px;
}
</style>
```

- **不需要 `defineComponent` 和 `setup()` 包裹**。

- **不需要手动 `return`变量和方法**，自动暴露给 `<template>` 使用。

- 更简洁，更易写，编译时性能更好。

---

### **3. 配置 Vite 插件**

- 在 `vite.config.ts` 中配置自动导入、组件注册、路由生成等插件

---

#### 3.1 修改 `vite.config.ts`

1. 打开 `vite.config.ts`，替换为以下内容：

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import VueRouter from 'unplugin-vue-router/vite'
import { TDesignResolver } from 'unplugin-vue-components/resolvers' // 桌面端
import vueJsx from '@vitejs/plugin-vue-jsx'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    vueJsx(),

    // 自动导入 API（如 ref、defineComponent 等）
    AutoImport({
      imports: ['vue', 'vue-router'],
      dts: 'src/auto-imports.d.ts', // 生成自动导入类型声明
    }),

    // 自动注册组件（配合 TDesign）
    Components({
      // resolvers: [TDesignResolver()], // 桌面端组件库
      resolvers: [
        TDesignResolver({
          library: 'tdesign-mobile-vue', // 指定移动端库
        }),
      ],
      dts: 'src/components.d.ts', // 生成自动注册类型声明
    }),

    // 自动生成路由（推荐配合 unplugin-vue-router）
    VueRouter({
      dts: 'src/typed-router.d.ts',
      routesFolder: 'src/pages', // 你需在 src/pages 中创建页面组件
    }),
  ],

  resolve: {
    alias: {
      '@': '/src',
    },
  },
})
```

2. `main.ts`文件中添加下列内容：

```ts
import 'tdesign-mobile-vue/es/style/index.css';
```

3. 为了配合 `unplugin-vue-router` 插件，请建立如下目录：

```bash
mkdir src/pages
```

4. 创建测试页面`pages/Home.vue`，如下：

```vue
<!-- unplugin-vue-router 插件占位 -->
<template>
    <div>首页</div>
</template>

<script setup lang="ts">
// 页面逻辑
</script>
```

#### 3.2 验证自动导入、组件注册、路由生成是否成功配置

1. 检验 `unplugin-auto-import` 是否工作，修改`src/components/HelloWorld.vue`文件内容如下：

   ```vue
   <script setup lang="ts">
   const count = ref(0)
   </script>
   
   <template>
     <div @click="count++">
       Count: {{ count }}
     </div>
   </template>
   ```

   没有手动 import `ref`，编译通过并运行正常。

2. 检验 `unplugin-vue-components` 是否工作，`App.vue`文件替换如下内容：

   ```vue
   <script setup lang="ts">
   import HelloWorld from './components/HelloWorld.vue'
   </script>
   
   <template>
     <div class="row">
       <t-button size="large" theme="primary" block>填充按钮</t-button>
     </div>
     <HelloWorld msg="Vite + Vue" />
   </template>
   
   <style scoped>
   .row {
     margin: 0 16px;
   }
   </style>
   ```

   `main.ts` 添加如下内容：

   ```ts
   // 引入 TDesign Mobile Vue 样式
   import 'tdesign-mobile-vue/es/style/index.css';
   ```

   `vite.config.ts`添加如下内容：

   ```ts
   import { TDesignResolver } from 'unplugin-vue-components/resolvers'
   
   AutoImport({
   	resolvers: [TDesignResolver({
   		library: 'mobile-vue'
   	})],
   // ...
   }),
   
   Components({
   	resolvers: [TDesignResolver({
   		library: 'mobile-vue'
   	})],
     // ...
   }),
   ```

   执行`npm run dev`，无需手动 import `TButton` ，页面能渲染出 TDesign 的按钮组件。

3. （待解决）检验 `unplugin-vue-router` 是否工作，main.ts作如下修改：

   ```ts
   // ...
   import { createRouter, createWebHistory } from 'vue-router/auto' // 自动路由
   // ...
   
   const app = createApp(App)
   const router = createRouter()
   app.use(router).mount('#app')
   ```

   再在App.vue中添加：

   ```vue
   <template>
     <router-view />
   </template>
   ```

   安装`vue-router`：

   ```bash
   npm install vue-router
   npm install --save-dev @types/vue-router
   
   // 删除缓存依赖
   rm -rf node_modules dist .vite
   
   // 重新安装依赖
   npm install
   
   // Cmd+Shift+P，输入并选择：Preferences: Open Workspace Settings (JSON)
   {
     "typescript.preferences.autoImportFileExcludePatterns": [
       "vue-router"
     ]
   }
   
   // "vite": "^7.0.4"改为"vite": "^6.0.0",
   
   // 使用mkdir -p src/pages新建文件目录，否则无法识别到，比如手动创建src/pages目录unplugin-vue-router插件就无法扫描到该目录
   
    vite.config.ts 的 VueRouter 配置里加上 routeBlockLang: undefined,
    
    Uncaught TypeError: Cannot read properties of undefined (reading 'forEach')
       at createRouterMatcher (chunk-5HSENCAP.js?v=29f3bf72:1272:10)
       at createRouter (chunk-5HSENCAP.js?v=29f3bf72:2338:19)
       at createRouter (auto:19:18)
       at main.ts:24:16
       
       
   typed-router.d.ts:
   declare module 'vue-router/auto-routes' {
     import type {
       RouteRecordInfo,
       ParamValue,
       ParamValueOneOrMore,
       ParamValueZeroOrMore,
       ParamValueZeroOrOne,
     } from 'vue-router'
   
     /**
      * Route name map generated by unplugin-vue-router
      */
     export interface RouteNamedMap {
       '/': RouteRecordInfo<'/', '/', Record<never, never>, Record<never, never>>,
     }
   }
   ```

   启动后浏览器访问 `/` 能自动加载 `pages/Home.vue`，表示 `unplugin-vue-router` 插件配置 **成功**

4. 

#### 3.2 一些错误及解决

##### 3.2.1 vite.config.ts文件出现：`找不到模块“@vitejs/plugin-vue-jsx”或其相应的类型声明。`

安装 `@vitejs/plugin-vue-jsx` 插件：

```bash
npm install -D @vitejs/plugin-vue-jsx
```

##### 3.2.2 vite.config.ts文件出现：`找不到模块“@vitejs/plugin-vue”或其相应的类型声明。`

安装`@vitejs/plugin-vue`插件：

```bash
npm install -D @vitejs/plugin-vue
```

##### 3.2.3 .vue文件代码全部是灰色

VS Code应用商店安装`Vue(Official)`插件。

#### 3.3 待解决

1. `vite.config.ts`配置路径别名，文件结构给ai，确定__dirname：https://juejin.cn/post/7386504498476744731#heading-5

2. 在 `.gitignore` 中加入自动生成的 `.d.ts` 文件路径，.gitignore文件是干什么的？整个初始化项目完成再修改.gitignore文件？：

   ```gitignore
   src/auto-imports.d.ts
   src/components.d.ts
   src/typed-router.d.ts
   ```

3. 补全 `src/router` 和 `src/pages` 示例代码?是配置vite这一步的？

#### 3.4 其他

##### 3.4.1 打印当前项目的目录结构，并排除 `node_modules` 目录下的子目录

```bash
tree -I 'node_modules' -L 3
```

---

### **4. 配置 ESLint**

- 使用 `@antfu/eslint-config`，保证符合风格（无分号、单引号等）
- 创建 `.eslintrc` 或 `.eslintrc.js`，配置 `eslint-plugin-vue` 等
- 添加 `lint`, `lint:fix` 命令
- 添加 ESLint 执行方式，如`.vscode/settings.json` 启用自动修复

------

#### 4.1 安装 ESLint 及相关依赖

执行以下命令：

```bash
npm install -D eslint @antfu/eslint-config
```

#### 4.2 创建 ESLint 配置文件

项目根目录新建 `eslint.config.js`：

```js
touch eslint.config.js
```

添加如下内容：

```js
// eslint.config.js
import antfu from '@antfu/eslint-config'

export default antfu()
```

#### 4.3 添加 Lint 脚本命令

 `package.json` 中添加以下脚本：

```json
"scripts": {
  "lint": "eslint . --ext .js,.ts,.vue",
  "lint:fix": "eslint . --ext .js,.ts,.vue --fix"
}
```

#### 4.4 配置 VSCode 自动修复（推荐）

创建或更新 `.vscode/settings.json`，添加以下内容：

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll": true,
    "source.fixAll.eslint": true
  },
  "eslint.validate": ["vue", "javascript", "typescript"],
  "editor.formatOnSave": false
}
```

#### 4.5 测试ESLint 配置是否生效

执行 `lint` 命令检查能输出项目中违反 ESLint 规则的文件列表

```bash
npm run lint
```

在比如`App.vue`文件中添加比如`const msg = "qqq"`双引号，执行下列命令能够自动修复部分内容：

```bash
npm run lint:fix
```

---

### **5. 配置 TypeScript**

- 检查和完善 `tsconfig.json`
  - 包括路径别名 `@/`、 `baseUrl`、`paths` 等
  - 配置 JSX、类型提示、strict 等选项

---

`tsconfig.json`添加如下内容：

```json
{
  "compilerOptions": {
    // 基本选项
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "lib": ["ESNext", "DOM"],
    "jsx": "preserve", 
    "allowJs": false,
    "strict": true,
    "noImplicitAny": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "isolatedModules": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "skipLibCheck": true,

    // 路径别名配置
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    },

    // 输出设置
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src", "env.d.ts", "vite.config.ts"],
  "exclude": ["node_modules", "dist"]
}
```

---

### 6. 配置自动路由（unplugin-vue-router）

- `src/pages/` 下的页面结构自动生成路由
- 创建 `router.ts` ，实现注册和挂载，在 `main.ts` 中挂载

---

根据官方文档配置：https://uvr.esm.is/introduction.html

后期需要 layout、权限等功能时，可以加工 `routes`，见官方文档Manipulating the routes部分；

---

### **7. 创建基础目录结构**

- `src/`
  - `pages/`：页面目录
  - `components/`：通用组件
  - `router/`：路由配置
  - `styles/`：样式
  - `utils/`：工具函数
  - `types/`：类型定义
  - `assets/`：静态资源
  - `App.vue`
  - `main.ts`

### **8. 创建 README 和目录说明**

- 补充 README.md 对项目结构的说明
- 可选：添加 `docs/` 或在 README 中写目录注释

### **9. 创建 Git 提交记录**

- 可以通过本地分支 + PR 的方式提交 `init: 工程化初始化` 的 Pull Request
