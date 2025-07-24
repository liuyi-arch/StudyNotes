### 1. Fork 原仓库

1. 打开原仓库地址：https://cnb.cool/tencent/tdesign/tdesign-mobile-vue-starter-community

2. 点击页面右上角的 Fork 按钮，将仓库 Fork 到自己的账号下。

------

### 2. 克隆 Fork 后的仓库到本地

在 cnb.cool 账号下找到 Fork 后的仓库，复制仓库地址，然后在本地执行：

```bash
git clone <你的仓库地址>
cd tdesign-mobile-vue-starter-community
```

------

### 3. 添加原仓库为上游（upstream）远程仓库

```bash
git remote add upstream https://cnb.cool/tencent/tdesign/tdesign-mobile-vue-starter-community.git
```

验证：

```bash
git remote -v
```

能够看到下列内容：

```bash
origin    https://cnb.cool/<你的用户名>/tdesign-mobile-vue-starter-community.git (fetch)
upstream  https://cnb.cool/tencent/tdesign/tdesign-mobile-vue-starter-community.git (fetch)
```

------

### 4. 创建新分支并进行开发

```bash
git checkout -b feature/initialization-and-engineering
# 进行开发和修改
```

------

### 5. 提交并推送到自己的远程仓库

```bash
git add .
git commit -m "feat: init"
git push origin feature/initialization-and-engineering
```

------

### 6. 在 Fork 仓库页面发起 Pull Request

1. 打开 Fork 后的仓库页面。
2. 会看到“Compare & pull request”按钮，点击它。
  ![image](https://github.com/user-attachments/assets/86761309-8a62-415a-b43e-602cbfec2b59)
3. 进入 PR 页面后，确认以下信息：
   - Base branch（目标分支）：应为 `main`
   - Compare branch（你的更改分支）：应为 `feature/initialization-and-engineering`
4. 点击页面右上的 **“New pull request”** 蓝色链接
   ![image](https://github.com/user-attachments/assets/e624f5e8-251b-459a-9157-3013703ace7e)
5. 填写 PR 标题和描述，确认目标分支为原仓库的 main（或指定分支）。
6. 点击红色按钮：`New pull request`，提交Pull Request。
  ![image](https://github.com/user-attachments/assets/d3096727-6ddf-49b4-9196-384bd104b7fd)

------

维护者对 PR 进行审核，审核通过，代码合并到主仓库。
