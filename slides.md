# VueUse 架构分享

基于 VueUse 项目架构的深入剖析

---

# 一、项目概述

**思考问题：你了解VueUse是什么吗？它能为Vue开发带来什么价值？**

## 1.1 项目定位

- **定义**：Vue Composition API 工具函数集合库
- **目标**：提供可复用的、类型安全的、Tree-shakeable 的 Vue 组合式函数
- **特点**：

<v-clicks>

- 🎪 交互式文档和演示
- ⚡ 完全 Tree-shakeable（按需引入）
- 🦾 强类型支持（TypeScript）
- 🔋 SSR 友好
- 🌎 无需打包工具（支持 CDN）
- 🔩 灵活配置（事件过滤器和目标）
- 🔌 可选插件生态（Router、Firebase、RxJS 等）

</v-clicks>

---

## 1.2 技术栈

<v-clicks>

- **包管理**：pnpm + workspace（Monorepo） - [pnpm](https://pnpm.io/)
- **构建工具**：tsdown（基于 esbuild） - [tsdown](https://tsdown.dev/), [esbuild](https://esbuild.github.io/)
- **构建系统**：Turbo（任务编排） - [Turbo](https://turbo.build/)
- **类型系统**：TypeScript 5.9+ - [TypeScript](https://www.typescriptlang.org/)
- **测试框架**：Vitest（单元测试 + 浏览器测试） - [Vitest](https://vitest.dev/)
- **文档系统**：VitePress - [VitePress](https://vitepress.dev/)
- **代码规范**：ESLint + Prettier - [ESLint](https://eslint.org/), [Prettier](https://prettier.io/)

</v-clicks>

---

# 二、Monorepo 架构设计

**思考问题：你知道Monorepo架构的优势是什么？VueUse为什么选择这种架构？**

## 2.0 Monorepo 演变史

### 2.0.1 传统多仓库模式的痛点

**早期软件开发**：每个项目独立仓库

<v-clicks>

- **依赖管理困难**：版本不一致，依赖冲突频繁
- **代码复用复杂**：跨项目共享代码需要发布npm包
- **开发体验差**：需要在多个仓库间切换，难以统一管理
- **发布协调难**：多个包的版本同步和发布顺序复杂

</v-clicks>

---

### 2.0.2 Monorepo 的诞生

**2000年代初**：Google、Babel 等公司开始探索

<v-clicks>

- **Google 的实践**：内部使用巨型仓库管理所有代码
- **Babel 6 (2015)**：首次在开源界大规模采用 Monorepo
- **Facebook 的贡献**：开源 Yarn Workspaces，加速 Monorepo 普及

</v-clicks>

<v-click>

**早期挑战**：

<v-clicks>

- 缺乏成熟的工具链支持
- 构建和测试效率低下
- 代码审查和分支管理复杂

</v-clicks>

</v-click>

---

### 2.0.3 工具链的进化

<v-click>

**第一阶段：基础工具 (2015-2017)**

<v-clicks>

- **Yarn Workspaces**：提供基础的多包管理能力
- **Lerna**：自动化版本管理和发布
- **Babel/Yarn 的成功**：证明 Monorepo 在开源界的可行性

</v-clicks>

</v-click>

<v-click>

**第二阶段：性能优化 (2018-2020)**

<v-clicks>

- **Rush**：微软推出的企业级 Monorepo 工具
- **Nx**：提供智能构建缓存和依赖分析
- **Turbo**：Vercel 推出的高性能构建系统

</v-clicks>

</v-click>

---

### 2.0.4 现代 Monorepo 生态

**2020年后**：Monorepo 成为主流

<v-clicks>

- **pnpm workspace**：高效的包管理器 + workspace 支持
- **Turbo/Nx**：智能缓存和并行构建
- **Changesets**：精细化的版本管理和发布
- **Turborepo**：云端缓存和远程缓存

</v-clicks>

<v-click>

**技术栈整合**：

<v-clicks>

- **包管理**：pnpm workspace (性能最佳)
- **构建缓存**：Turbo (速度最快)
- **版本管理**：Changesets (流程最优)
- **代码质量**：统一的 ESLint/Prettier 配置

</v-clicks>

</v-click>

---

### 2.0.5 为什么 Monorepo 成为主流？

<v-click>

**技术优势**：

<v-clicks>

- **依赖统一**：消除版本冲突，简化依赖管理
- **代码复用**：内部包可直接引用，无需发布
- **原子提交**：跨包变更可在一个提交中完成
- **开发效率**：统一的工具链和开发环境

</v-clicks>

</v-click>

<v-click>

**工程优势**：

<v-clicks>

- **团队协作**：减少仓库间切换，提高开发效率
- **质量保证**：统一的测试和代码规范
- **发布协调**：自动化处理包间依赖和发布顺序
- **可维护性**：集中管理配置和文档

</v-clicks>

</v-click>

---

### 2.0.6 VueUse 的选择

<v-click>

**为什么选择 Monorepo？**

<v-clicks>

- **多包架构**：11+ 个功能包需要统一管理
- **频繁更新**：函数迭代需要跨包协调
- **质量要求**：统一的测试和构建标准
- **生态建设**：便于扩展新的集成包

</v-clicks>

</v-click>

<v-click>

**技术选型**：

<v-clicks>

- **pnpm workspace**：高效的包管理和磁盘利用
- **Turbo**：并行构建和智能缓存
- **tsdown**：现代化的 TypeScript 构建工具
- **Vitest**：统一的测试框架

</v-clicks>

</v-click>

---

## 2.1 项目整体结构

VueUse 采用 Monorepo 架构，将所有包和工具组织在一个仓库中，便于统一管理和维护。

<v-click>

**主要特点：**

<v-clicks>

- 📦 **多包管理**：11+ 个功能包
- 🔧 **自动化工具**：完整的构建、测试、发布流程
- 📚 **文档系统**：自动生成和部署
- 🧪 **测试体系**：单元测试 + 浏览器测试 + 类型测试

</v-clicks>

</v-click>

---

### 2.1.1 核心包结构

<v-click>

```text
packages/
├── shared/              # 共享工具函数（无 Vue 依赖）
├── core/                # 核心包（主要功能集合）
```

</v-click>

<v-click>

```text
packages/
├── shared/              # 共享工具函数（无 Vue 依赖）
├── core/                # 核心包（主要功能集合）
├── components/          # 无渲染组件版本
├── integrations/        # 第三方库集成（axios、fuse.js 等）
├── math/                # 数学计算工具
```

</v-click>

<v-click>

```text
packages/
├── shared/              # 共享工具函数（无 Vue 依赖）
├── core/                # 核心包（主要功能集合）
├── components/          # 无渲染组件版本
├── integrations/        # 第三方库集成（axios、fuse.js 等）
├── math/                # 数学计算工具
├── router/              # Vue Router 集成
├── rxjs/                # RxJS 集成
├── firebase/            # Firebase 集成
├── electron/            # Electron 集成
├── nuxt/                # Nuxt 模块
```

</v-click>

<v-click>

```text
packages/
├── shared/              # 共享工具函数（无 Vue 依赖）
├── core/                # 核心包（主要功能集合）
├── components/          # 无渲染组件版本
├── integrations/        # 第三方库集成（axios、fuse.js 等）
├── math/                # 数学计算工具
├── router/              # Vue Router 集成
├── rxjs/                # RxJS 集成
├── firebase/            # Firebase 集成
├── electron/            # Electron 集成
├── nuxt/                # Nuxt 模块
├── metadata/            # 元数据管理（函数信息、类型定义）
├── .vitepress/          # VitePress 文档配置
├── .test/               # 测试配置
└── public/              # 静态资源
```

</v-click>

---

### 2.1.2 脚本和工具目录

```text
scripts/                 # 构建和发布脚本
├── changelog.ts         # 变更日志生成
├── clean.ts             # 清理脚本
├── export-size.ts       # 包大小分析
├── publish.ts           # 发布脚本
├── redirects.ts         # 重定向配置
├── release.ts           # 发布准备
├── update.ts            # 更新脚本
└── utils.ts             # 工具函数

test/                    # 测试相关
├── exports.test.ts      # 导出测试
└── exports/             # 导出测试文件

types/                   # 类型定义
playgrounds/             # 游乐场/示例项目
├── build.sh             # 构建脚本
├── nuxt/                # Nuxt 示例
└── vite/                # Vite 示例

patches/                 # 补丁文件
└── google-font-installer@1.2.0.patch
```

---

### 2.1.3 配置文件

```text
package.json             # 项目配置
├── scripts              # npm 脚本定义
├── devDependencies      # 开发依赖
└── resolutions          # 依赖版本锁定

pnpm-workspace.yaml      # Workspace 配置
├── packages             # 包路径定义
├── catalogs             # 依赖版本目录
└── neverBuiltDependencies  # 不构建依赖

turbo.json               # Turbo 构建配置
├── tasks.build          # 构建任务
└── outputs              # 输出文件定义

tsconfig.json            # TypeScript 配置
├── compilerOptions      # 编译选项
├── include              # 包含文件
└── exclude              # 排除文件
```

---

### 2.1.4 文档和规范文件

```text
README.md                # 项目文档
CONTRIBUTING.md          # 贡献指南
LICENSE                  # 许可证
CODE_OF_CONDUCT.md       # 行为准则

.editorconfig            # 编辑器配置
.gitignore               # Git 忽略文件
.vscode/                 # VS Code 配置
├── extensions.json      # 推荐扩展
└── settings.json        # 编辑器设置

.attw.json               # 类型检查配置
netlify.toml             # Netlify 部署配置
unocss.config.ts         # UnoCSS 配置
taze.config.ts           # 依赖更新配置
eslint.config.js         # ESLint 配置
vitest.config.ts         # Vitest 测试配置
tsdown.config.ts         # tsdown 构建配置
```

---

## 2.2 Workspace 配置

VueUse 的 Workspace 配置是 Monorepo 架构的核心，整合了包管理、构建和任务编排等关键工具。

---

### 2.2.1 包管理

**pnpm workspace** - Monorepo 包管理器

<v-clicks>

- **作用**：管理多包仓库，统一安装和链接依赖
- **优势**：磁盘空间节省、安装速度快、严格的依赖隔离
- **配置**：通过 `pnpm-workspace.yaml` 定义包路径和依赖目录
- **特性**：
  - 📦 **Catalog 管理**：统一依赖版本，避免版本冲突
  - 🔗 **智能链接**：自动处理包间依赖关系
  - 🚀 **并行安装**：充分利用网络和磁盘 I/O

</v-clicks>

---

### 2.2.2 构建工具

**tsdown** - 现代化构建工具

<v-clicks>

- **作用**：基于 rolldown 的快速打包工具，专为 TypeScript 项目优化
- **特性**：
  - ⚡ 超快构建速度（rolldown 驱动）
  - 📦 多格式输出（ESM、IIFE、类型声明）
  - 🎯 Tree-shaking 友好
  - 🔧 零配置开箱即用
- **输出格式**：
  - `.mjs` - ES Module（支持 Tree-shaking）
  - `.iife.js` - 立即执行函数（CDN 友好）
  - `.d.mts` - TypeScript 声明文件

</v-clicks>

---

### 2.2.3 任务编排

**Turbo** - 构建任务编排器

<v-clicks>

- **作用**：优化 Monorepo 的构建流程，提供缓存和并行执行
- **特性**：
  - 🚀 智能缓存（基于文件内容哈希）
  - ⚡ 并行构建（充分利用多核 CPU）
  - 📊 依赖图分析（优化构建顺序）
  - 🎯 增量构建（只构建变更部分）
- **配置**：`turbo.json` 定义任务和缓存策略
- **优势**：显著提升大型 Monorepo 的构建效率

</v-clicks>

---

# 三、构建系统架构

**思考问题：你了解VueUse的构建系统是如何工作的吗？元数据系统扮演什么角色？**

## 3.1 元数据系统

VueUse 的元数据系统是整个项目的核心，它管理着所有函数的信息、分类和配置，确保项目的一致性和自动化。

---

### 3.1.1 元数据模块架构

```text
packages/metadata/
├── index.json          # 元数据 JSON 文件（核心数据）
├── metadata.ts         # 元数据处理逻辑
├── types.ts            # TypeScript 类型定义
├── utils.ts            # 工具函数
└── scripts/            # 生成脚本
```

---

### 3.1.2 元数据核心作用

<v-click>

**📊 函数信息管理**

<v-clicks>

- 收集所有 VueUse 函数的元信息（名称、包、分类、描述）
- 维护函数依赖关系和类型定义
- 提供统一的函数查询和索引

</v-clicks>

</v-click>

<v-click>

**🔄 自动化生成**

<v-clicks>

- 自动生成各包的导出文件 (`index.ts`)
- 生成函数文档和演示代码
- 更新 README 和贡献者信息

</v-clicks>

</v-click>

<v-click>

**📚 文档系统支持**

<v-clicks>

- 为 VitePress 提供结构化数据
- 生成函数列表和分类导航
- 维护文档页脚和徽章信息

</v-clicks>

</v-click>

---

### 3.1.3 元数据生成流程

<v-click>

```text
源代码扫描 (packages/*/src/**/*.ts)
```

</v-click>

<v-click>

```text
源代码扫描 (packages/*/src/**/*.ts)
    ↓
提取函数信息 (JSDoc + TypeScript)
```

</v-click>

<v-click>

```text
源代码扫描 (packages/*/src/**/*.ts)
    ↓
提取函数信息 (JSDoc + TypeScript)
    ↓
分类整理 (categories, packages, functions)
```

</v-click>

<v-click>

```text
源代码扫描 (packages/*/src/**/*.ts)
    ↓
提取函数信息 (JSDoc + TypeScript)
    ↓
分类整理 (categories, packages, functions)
    ↓
生成 index.json (packages/metadata/index.json)
```

</v-click>

---

## 3.2 自动化更新系统（nr update）

`nr update` 是 VueUse 的核心自动化脚本，负责将元数据同步到代码和文档中，确保项目的一致性。

---

### 3.2.1 执行流程

<v-click>

```text
nr update
```

</v-click>

<v-click>

```text
nr update
    ↓
1. turbo run update -F @vueuse/metadata  (生成元数据)
```

</v-click>

<v-click>

```text
nr update
    ↓
1. turbo run update -F @vueuse/metadata  (生成元数据)
    ↓
2. tsx scripts/update.ts  (执行更新脚本)
```

</v-click>

<v-click>

```text
nr update
    ↓
1. turbo run update -F @vueuse/metadata  (生成元数据)
    ↓
2. tsx scripts/update.ts  (执行更新脚本)
    ↓
并行执行 8 个更新任务
```

</v-click>

---

### 3.2.2 元数据生成阶段

**turbo run update -F @vueuse/metadata**

- **作用**：调用 metadata 包的构建任务
- **输入**：扫描所有包的源代码文件
- **输出**：生成 `packages/metadata/index.json`
- **配置**：`packages/metadata/turbo.json`

---

### 3.2.3 更新执行阶段

**tsx scripts/update.ts**

- **入口文件**：`scripts/update.ts`
- **核心逻辑**：并行执行 8 个更新函数
- **数据源**：使用生成的 metadata

---

### 3.2.4 更新模块详解

<v-click>

**🔗 updateImport (导出文件生成)**

<v-clicks>

- **作用**：为每个包生成 `index.ts` 导出文件
- **逻辑**：根据包的函数列表生成 ES Module 导出
- **位置**：`packages/*/index.ts`

</v-clicks>

</v-click>

<v-click>

**📖 updatePackageREADME (包文档)**

<v-clicks>

- **作用**：更新各包的 README.md
- **内容**：函数列表、安装说明、使用示例
- **位置**：`packages/*/README.md`

</v-clicks>

</v-click>

<v-click>

**🏠 updateIndexREADME (根文档)**

<v-clicks>

- **作用**：更新项目根目录的 README.md
- **内容**：项目介绍、函数统计、贡献者信息

</v-clicks>

</v-click>

<v-click>

**📋 updateFunctionsMD (函数列表)**

<v-clicks>

- **作用**：生成 `packages/functions.md`
- **内容**：所有函数的完整列表和链接

</v-clicks>

</v-click>

<v-click>

**📄 updateFunctionREADME (函数文档)**

<v-clicks>

- **作用**：为每个函数生成独立的 README.md
- **内容**：函数描述、参数、返回值、示例

</v-clicks>

</v-click>

<v-click>

**⚙️ updatePackageJSON (包配置)**

<v-clicks>

- **作用**：同步包的 package.json 配置
- **内容**：版本号、exports 字段、依赖信息

</v-clicks>

</v-click>

<v-click>

**🏷️ updateCountBadge (统计徽章)**

<v-clicks>

- **作用**：更新函数数量徽章
- **位置**：README.md 中的徽章显示

</v-clicks>

</v-click>

<v-click>

**👥 updateContributors (贡献者)**

<v-clicks>

- **作用**：更新贡献者列表和头像
- **条件**：仅在 Netlify 环境执行

</v-clicks>

</v-click>

---

# 四、文档系统架构

**思考问题：VueUse的文档系统有什么独特之处？自动化文档生成是如何实现的？**

VueUse 的文档系统基于 VitePress，但采用了高度自动化的独特实现方式，与传统的手动编写文档的项目有显著区别。

---

## 4.1 VitePress 集成特性

### 4.1.1 自动化文档生成

<v-click>

**与其他项目的区别**：

<v-clicks>

- **传统项目**：手动编写每个页面的 Markdown 文档
- **VueUse**：文档内容从元数据自动生成，函数更新时自动同步

</v-clicks>

</v-click>

<v-click>

**实现方式**：

<v-clicks>

- **元数据驱动**：所有文档内容来自 `packages/metadata/index.json`
- **模板渲染**：使用 Vue 组件动态渲染函数列表和详情
- **自动更新**：`nr update` 脚本同步文档内容

</v-clicks>

</v-click>

---

### 4.1.2 高度定制化的主题系统

```text
packages/.vitepress/theme/
├── components/          # 自定义 Vue 组件
├── composables/         # Vue 组合式函数
├── styles/              # 自定义样式
└── index.ts             # 主题入口
```

<v-click>

**独特功能**：

<v-clicks>

- **FunctionsList 组件**：动态渲染所有函数
- **Demo 组件**：内嵌 Vue 演示代码
- **TypeScript 类型提示**：集成 Shikiji Twoslash

</v-clicks>

</v-click>

---

### 4.1.3 插件生态系统

```text
packages/.vitepress/plugins/
├── markdownTransform.ts # Markdown 转换插件
├── contributors.ts      # 贡献者数据插件
├── changelog.ts         # 变更日志插件
└── pwa-virtual.ts       # PWA 虚拟模块插件
```

<v-click>

**核心插件功能**：

<v-clicks>

- **实时类型检查**：代码示例中的 TypeScript 错误提示
- **贡献者展示**：自动获取和展示贡献者信息
- **PWA 支持**：离线访问和安装功能

</v-clicks>

</v-click>

---

## 4.2 VueUse VitePress 模式的优势

### 4.2.1 主要优点

<v-click>

**🔄 自动化维护**

<v-clicks>

- 函数更新时文档自动同步，无需手动维护
- 保证文档与代码的一致性，避免过时信息
- 减少重复劳动，提高维护效率

</v-clicks>

</v-click>

<v-click>

**🎯 高度一致性**

<v-clicks>

- 统一的文档格式和风格
- 自动生成函数签名、参数说明
- 标准化演示代码和使用示例

</v-clicks>

</v-click>

<v-click>

**🚀 丰富的交互体验**

<v-clicks>

- 实时 TypeScript 类型提示
- 可执行的代码演示
- PWA 离线访问支持

</v-clicks>

</v-click>

<v-click>

**📊 数据驱动**

<v-clicks>

- 基于元数据的结构化文档
- 支持复杂的查询和筛选功能
- 便于生成统计信息和分析报告

</v-clicks>

</v-click>

---

## 4.3 适用场景分析

### 4.3.1 推荐使用场景

- **工具库项目**：如 VueUse、Lodash 等 API 密集的项目
- **组件库**：需要大量演示和交互的项目
- **大型 Monorepo**：多个包需要统一文档管理

### 4.3.2 不推荐场景

- **小型项目**：简单的静态文档更合适
- **内容型网站**：博客、教程等手动编写内容为主的项目
- **快速原型**：需要快速搭建文档的场景

---

# 五、开发工作流 & 贡献实战

**思考问题：你想知道VueUse的开发流程是怎样的吗？如何为VueUse贡献代码？**

## 5.1 开发流程

<v-clicks>

1. **创建函数**：使用模板生成
2. **实现功能**：编写 `index.ts`
3. **编写测试**：`index.test.ts`
4. **编写文档**：`index.md` + `demo.vue`
5. **提交 PR**：自动检查

</v-clicks>

---

## 5.2 自动化

<v-clicks>

- **构建**：Turbo 并行构建
- **测试**：自动运行测试
- **类型检查**：TypeScript 类型检查
- **代码规范**：ESLint + Prettier
- **文档生成**：自动生成导出和文档

</v-clicks>
  
---

# 附录：关键文件说明

## A.1 配置文件

- `package.json`：根配置、脚本定义
- `pnpm-workspace.yaml`：Workspace 配置
- `turbo.json`：Turbo 构建配置
- `tsdown.config.ts`：构建工具配置
- `tsconfig.json`：TypeScript 配置

---

## A.2 核心目录

- `packages/core/`：核心函数实现
- `packages/shared/`：共享工具函数
- `packages/metadata/`：元数据管理
- `scripts/`：构建和发布脚本

---

## A.3 模板文件

- `packages/core/_template/`：新函数模板

---

# 七、测试系统架构

**思考问题：VueUse是如何保证代码质量的？测试系统是如何设计的？**

## 7.1 测试框架

<v-clicks>

- **单元测试**：Vitest + @vue/test-utils
- **浏览器测试**：Vitest + Playwright
- **类型测试**：@arethetypeswrong/cli
- **覆盖率**：@vitest/coverage-v8

</v-clicks>

---

## 7.2 测试配置

<v-clicks>

- **vitest.config.ts**：测试配置
- **测试文件**：`*.test.ts`
- **浏览器环境**：jsdom / happy-dom
- **CI/CD**：GitHub Actions

</v-clicks>

---

## 7.3 测试分类

<v-clicks>

- **单元测试**：函数逻辑测试
- **集成测试**：跨函数交互测试
- **E2E 测试**：浏览器端功能测试
- **类型测试**：TypeScript 类型检查

</v-clicks>

---

# 八、发布与分发系统

**思考问题：VueUse是如何发布和分发的？自动化发布流程是怎样的？**

## 8.1 发布流程

<v-clicks>

1. **版本管理**：bumpp 自动版本递增
2. **构建**：Turbo 并行构建所有包
3. **发布**：tsx scripts/publish.ts
4. **文档部署**：Netlify / Vercel

</v-clicks>

---

## 8.2 分发渠道

<v-clicks>

- **npm**：ES Module + IIFE
- **CDN**：unpkg / jsdelivr
- **文档**：vueuse.org

</v-clicks>

---

## 8.3 自动化脚本

<v-clicks>

- **scripts/publish.ts**：发布脚本
- **scripts/release.ts**：发布准备
- **scripts/changelog.ts**：变更日志生成
- **scripts/export-size.ts**：包大小分析

</v-clicks>

---

感谢观看！

如有问题，请随时提问。
