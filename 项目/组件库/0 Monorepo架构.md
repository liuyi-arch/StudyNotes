# 将组件库迁移到 Monorepo 架构完整指南

## 🎯 迁移策略选择

### 方案对比

| 工具 | 复杂度 | 性能 | 生态 | 适用场景 |
|------|--------|------|------|----------|
| **pnpm workspaces** | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | 小-中型项目 |
| **Nx** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | 大型企业项目 |
| **Rush** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | 超大规模项目 |
| **Lerna + yarn/npm** | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | 传统选择 |

## 🚀 推荐方案：pnpm + Nx

基于项目特点，推荐使用 **pnpm workspaces + Nx** 的组合方案。

---

## 📋 迁移步骤

### Step 1: 项目结构重组

```bash
# 原始结构
react-vite-component-template/
├── src/components/
│   ├── MyButton/
│   ├── MyCounter/
│   └── MyTitle/
└── package.json

# 目标结构
react-component-monorepo/
├── packages/
│   ├── button/
│   ├── counter/
│   ├── title/
│   ├── shared/           # 共享工具
│   └── tokens/           # 设计令牌
├── apps/
│   ├── storybook/        # Storybook 应用
│   └── playground/       # 测试应用
├── tools/
│   └── build-scripts/
├── package.json          # 根配置
└── pnpm-workspace.yaml
```

### Step 2: 安装工具

```bash
# 全局安装
npm install -g pnpm @nrwl/cli

# 创建新的 monorepo
npx create-nx-workspace@latest react-components \
  --preset=npm \
  --packageManager=pnpm
```

### Step 3: 根配置文件

**pnpm-workspace.yaml**
```yaml
packages:
  - 'packages/*'
  - 'apps/*'
  - 'tools/*'
```

**package.json** (根目录)
```json
{
  "name": "react-components-monorepo",
  "private": true,
  "scripts": {
    "build": "nx run-many --target=build --all",
    "test": "nx run-many --target=test --all",
    "lint": "nx run-many --target=lint --all",
    "dev:storybook": "nx dev storybook",
    "build:storybook": "nx build storybook"
  },
  "devDependencies": {
    "@nrwl/workspace": "latest",
    "@nrwl/react": "latest",
    "@nrwl/vite": "latest",
    "@nrwl/storybook": "latest",
    "nx": "latest",
    "typescript": "^5.0.0"
  }
}
```

### Step 4: 迁移组件

#### 4.1 Button 组件包

**packages/button/package.json**
```json
{
  "name": "@mylib/button",
  "version": "1.0.0",
  "main": "dist/index.js",
  "module": "dist/index.esm.js",
  "types": "dist/index.d.ts",
  "files": ["dist"],
  "scripts": {
    "build": "vite build",
    "test": "vitest",
    "lint": "eslint src --ext .ts,.tsx"
  },
  "peerDependencies": {
    "react": "^18.0.0 || ^19.0.0",
    "react-dom": "^18.0.0 || ^19.0.0"
  },
  "devDependencies": {
    "@mylib/shared": "workspace:*",
    "@mylib/tokens": "workspace:*"
  }
}
```

**packages/button/vite.config.ts**
```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import dts from 'vite-plugin-dts'
import { resolve } from 'path'

export default defineConfig({
  plugins: [
    react(),
    dts({ insertTypesEntry: true })
  ],
  build: {
    lib: {
      entry: resolve(__dirname, 'src/index.ts'),
      name: 'MyLibButton',
      formats: ['es', 'cjs'],
      fileName: (format) => `index.${format === 'es' ? 'esm' : format}.js`
    },
    rollupOptions: {
      external: ['react', 'react-dom'],
      output: {
        globals: {
          react: 'React',
          'react-dom': 'ReactDOM'
        }
      }
    }
  }
})
```

#### 4.2 共享包结构

**packages/shared/package.json**
```json
{
  "name": "@mylib/shared",
  "version": "1.0.0",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "exports": {
    ".": {
      "import": "./dist/index.esm.js",
      "require": "./dist/index.js",
      "types": "./dist/index.d.ts"
    },
    "./utils": {
      "import": "./dist/utils.esm.js",
      "require": "./dist/utils.js",
      "types": "./dist/utils.d.ts"
    }
  }
}
```

**packages/tokens/src/index.ts** (设计令牌)
```typescript
export const colors = {
  primary: 'crimson',
  secondary: '#666',
  background: '#fff'
} as const

export const spacing = {
  xs: '0.25rem',
  sm: '0.5rem',
  md: '1rem',
  lg: '1.5rem',
  xl: '2rem'
} as const

export const typography = {
  fontSizes: {
    sm: '0.875rem',
    base: '1rem',
    lg: '1.125rem',
    xl: '1.25rem'
  }
} as const
```

### Step 5: Nx 配置

**nx.json**
```json
{
  "extends": "nx/presets/npm.json",
  "$schema": "./node_modules/nx/schemas/nx-schema.json",
  "targetDefaults": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["{projectRoot}/dist"]
    },
    "test": {
      "inputs": ["default", "^production"]
    }
  },
  "namedInputs": {
    "default": ["{projectRoot}/**/*", "sharedGlobals"],
    "production": [
      "default",
      "!{projectRoot}/**/?(*.)+(spec|test).[jt]s?(x)?(.snap)",
      "!{projectRoot}/tsconfig.spec.json"
    ],
    "sharedGlobals": ["{workspaceRoot}/babel.config.json"]
  },
  "generators": {
    "@nrwl/react": {
      "library": {
        "style": "css",
        "linter": "eslint",
        "bundler": "vite"
      }
    }
  }
}
```

**packages/button/project.json**
```json
{
  "name": "button",
  "$schema": "../../node_modules/nx/schemas/project-schema.json",
  "sourceRoot": "packages/button/src",
  "projectType": "library",
  "targets": {
    "build": {
      "executor": "@nrwl/vite:build",
      "outputs": ["{options.outputPath}"],
      "options": {
        "outputPath": "packages/button/dist"
      }
    },
    "test": {
      "executor": "@nrwl/vite:test",
      "options": {
        "passWithNoTests": true
      }
    },
    "lint": {
      "executor": "@nrwl/eslint:lint",
      "outputs": ["{options.outputFile}"],
      "options": {
        "lintFilePatterns": ["packages/button/**/*.{ts,tsx,js,jsx}"]
      }
    }
  },
  "tags": ["type:lib", "scope:ui"]
}
```

### Step 6: Storybook 集成

**apps/storybook/.storybook/main.ts**
```typescript
import type { StorybookConfig } from '@storybook/react-vite'

const config: StorybookConfig = {
  stories: [
    '../../../packages/*/src/**/*.stories.@(js|jsx|ts|tsx|mdx)',
  ],
  addons: [
    '@storybook/addon-essentials',
    '@storybook/addon-interactions',
  ],
  framework: {
    name: '@storybook/react-vite',
    options: {},
  },
  viteFinal: async (config) => {
    // 配置别名以支持 workspace packages
    config.resolve = {
      ...config.resolve,
      alias: {
        ...config.resolve?.alias,
        '@mylib/button': require.resolve('../../../packages/button/src'),
        '@mylib/counter': require.resolve('../../../packages/counter/src'),
        '@mylib/title': require.resolve('../../../packages/title/src'),
      }
    }
    return config
  }
}

export default config
```

### Step 7: 自动化脚本

**tools/build-scripts/build-all.ts**
```typescript
import { execSync } from 'child_process'
import { readFileSync, writeFileSync } from 'fs'
import { join } from 'path'

const packages = ['shared', 'tokens', 'button', 'counter', 'title']

async function buildAll() {
  console.log('🚀 开始构建所有包...')
  
  for (const pkg of packages) {
    console.log(`📦 构建 ${pkg}...`)
    try {
      execSync(`nx build ${pkg}`, { stdio: 'inherit' })
      console.log(`✅ ${pkg} 构建完成`)
    } catch (error) {
      console.error(`❌ ${pkg} 构建失败:`, error)
      process.exit(1)
    }
  }
  
  console.log('🎉 所有包构建完成!')
}

buildAll()
```

### Step 8: 发布配置

**tools/release/release.ts**
```typescript
import { execSync } from 'child_process'
import semver from 'semver'

interface ReleaseOptions {
  version?: string
  tag?: string
  packages?: string[]
}

async function release(options: ReleaseOptions = {}) {
  const { version = 'patch', tag = 'latest', packages } = options
  
  // 1. 运行测试
  console.log('🧪 运行测试...')
  execSync('nx run-many --target=test --all', { stdio: 'inherit' })
  
  // 2. 构建所有包
  console.log('🏗️ 构建所有包...')
  execSync('nx run-many --target=build --all', { stdio: 'inherit' })
  
  // 3. 版本管理
  console.log('📝 更新版本...')
  const targetPackages = packages || ['button', 'counter', 'title', 'shared', 'tokens']
  
  for (const pkg of targetPackages) {
    execSync(`cd packages/${pkg} && npm version ${version}`, { stdio: 'inherit' })
  }
  
  // 4. 发布
  console.log('🚀 发布包...')
  for (const pkg of targetPackages) {
    execSync(`cd packages/${pkg} && npm publish --tag ${tag}`, { stdio: 'inherit' })
  }
  
  console.log('✅ 发布完成!')
}

// 使用示例
// release({ version: 'minor', packages: ['button'] })
```

---

## 🔧 开发工作流

### 日常开发命令

```bash
# 安装依赖
pnpm install

# 开发模式
pnpm dev:storybook          # 启动 Storybook
nx dev button               # 开发特定组件

# 构建
nx build button             # 构建单个包
nx run-many --target=build --all  # 构建所有包

# 测试
nx test button              # 测试单个包
nx run-many --target=test --all   # 测试所有包

# 代码质量
nx lint button              # 检查单个包
nx run-many --target=lint --all   # 检查所有包

# 依赖图分析
nx graph                    # 查看项目依赖关系
```

### 添加新组件

```bash
# 使用 Nx 生成器创建新组件
nx generate @nrwl/react:library --name=input --directory=packages --buildable=true

# 或手动创建
mkdir packages/input
cd packages/input
pnpm init
# 配置 package.json, vite.config.ts 等
```

---

## 📊 迁移后的优势

### ✅ 开发体验改善
- **🔍 智能缓存**: Nx 只构建变更的包
- **📈 依赖图**: 清晰的包依赖关系
- **🎯 并行执行**: 多包并行构建/测试
- **🔧 统一工具链**: 共享配置和脚本

### ✅ 发布管理优化
- **🔄 独立版本**: 每个组件独立发版
- **📦 按需安装**: 用户只安装需要的组件
- **🏷️ 语义化版本**: 自动化版本管理
- **📋 变更日志**: 自动生成发布说明

### ✅ 团队协作提升
- **👥 模块化开发**: 不同团队负责不同包
- **🚀 渐进升级**: 减少破坏性变更影响
- **🔒 类型安全**: TypeScript 跨包类型检查
- **📚 文档集成**: Storybook 统一文档

---

## ⚠️ 迁移注意事项

### 常见问题和解决方案

1. **依赖冲突**
   ```bash
   # 使用 pnpm 解决 phantom dependencies
   pnpm install --shamefully-hoist=false
   ```

2. **构建顺序**
   ```json
   // 在 nx.json 中正确配置依赖
   "targetDefaults": {
     "build": {
       "dependsOn": ["^build"]
     }
   }
   ```

3. **类型引用**
   ```typescript
   // 使用 workspace: 协议引用本地包
   "dependencies": {
     "@mylib/tokens": "workspace:*"
   }
   ```

### 渐进式迁移策略

1. **Phase 1**: 创建 monorepo 结构，保持原有构建
2. **Phase 2**: 逐个迁移组件，测试发布流程
3. **Phase 3**: 优化构建，启用缓存和并行
4. **Phase 4**: 完善工具链，自动化发布

通过这种方式，可以平滑地将现有组件库迁移到 Monorepo 架构，获得更好的开发体验和维护性。
