## **组件库和 NPM 私有包**
如果企业内多个项目需要用到相同的 UI 组件库，可以搭建自己的 NPM 私有库：
```bash
npm init
npm publish --access=restricted  # 发布到私有 npm
```
> **团队成员可以直接 `npm install @company/ui-library` 进行复用，避免重复造轮子。**
