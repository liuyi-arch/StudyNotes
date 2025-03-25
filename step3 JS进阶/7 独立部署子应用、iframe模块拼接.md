## **微前端架构（适用于大型前端项目）**
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
