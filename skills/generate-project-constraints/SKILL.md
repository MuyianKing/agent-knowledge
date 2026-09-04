---
name: "generate-project-constraints"
description: "详细分析项目并生成 AGENTS.md 约束文件以提高 AI 缓存命中率。当用户说\"帮我给项目建立约束文件\"、\"生成项目约束\"、\"建立 agent 规则\"等类似意图时立即触发。"
---

# Generate Project Constraints (生成项目约束文件)

本 Skill 用于对当前工作区进行**详细、系统性的项目扫描**，并据此生成（或更新）根目录下的 `AGENTS.md` 约束文件。该文件作为 AI Coding Agent 的稳定上下文，可显著提高生成代码的一致性与提示词缓存命中率。

## 触发条件

当用户输入以下或语义等价的指令时，**立即且完整**执行本流程：

- "帮我给项目建立约束文件"
- "生成项目约束 / 生成 AGENTS.md / 建立 agent 规则"
- "详细的过一遍项目……生成约束文件，提高缓存命中率"
- "给项目加一份 AI 上下文规则"

## 执行步骤（必须严格依次执行）

### Step 1 · 项目结构扫描

1. 使用 `LS` 列出仓库根目录与 `src/`、`.vscode/`、`typings/`、`.trae/` 等关键目录的一级结构。
2. 使用 `Glob` 检索以下文件是否存在（用于判定技术栈与既有约束）：
   - 包管理：`package.json`、`pnpm-lock.yaml`、`yarn.lock`、`package-lock.json`、`bun.lockb`、`pnpm-workspace.yaml`、`.npmrc`、`.nvmrc`
   - 构建：`vite.config.*`、`webpack.config.*`、`rollup.config.*`、`rsbuild.config.*`、`turbo.json`、`nx.json`
   - 语言：`tsconfig.json`、`jsconfig.json`、`*.d.ts`
   - Lint/Format：`eslint.config.*`、`.eslintrc*`、`.prettierrc*`、`prettier.config.*`、`biome.json`、`oxlintrc.*`、`.editorconfig`、`stylelint.config.*`
   - 原子化 CSS：`uno.config.*`、`tailwind.config.*`、`windi.config.*`、`postcss.config.*`
   - 框架：`next.config.*`、`nuxt.config.*`、`vue.config.*`、`vite.config.*`、`astro.config.*`、`taro.config.*`、`app.json`、`manifest.json`、`pages.json`、`unh.config.*`
   - Mono Repo：`pnpm-workspace.yaml`、`lerna.json`、`turbo.json`
   - 测试：`vitest.config.*`、`jest.config.*`、`playwright.config.*`、`cypress.config.*`
   - CI：`.github/workflows/*`、`.gitlab-ci.yml`
   - 其他：`.gitignore`、`.dockerignore`、`Dockerfile`、`README*`

### Step 2 · 关键文件深读

对存在的文件**逐一使用 `Read` 读取完整内容**（不要只读片段），重点收集：

- `package.json`：`name` / `version` / `type`（ESM 或 CJS）/ `scripts` / `dependencies` / `devDependencies` / `engines` / `packageManager` / `overrides` / `resolutions`
- 构建配置：插件列表、alias、target、SSR/SSG、自动导入与组件解析器
- Lint/Prettier：基础预设、override 规则、是否禁用 Prettier
- 原子化 CSS：使用的 preset / transformer / shortcuts / 图标集
- 框架入口：`main.{js,ts}` / `App.{vue,tsx}` / 路由配置 / 状态管理注册
- `tsconfig.json` 或 `jsconfig.json`：路径别名、types 集合
- `.vscode/settings.json`：保存动作、格式化器、ESLint validate 列表
- `manifest.json` / `pages.json`（uni-app/小程序）
- `.npmrc` / workspace 配置

### Step 3 · 信息聚合（建立技术栈画像）

整理出以下维度的事实清单（用于写入 AGENTS.md）：

| 维度 | 说明 |
| ---- | ---- |
| 项目定位 | 应用类型（Web/小程序/桌面/Node/SDK/Monorepo）与业务领域 |
| 编程语言 | TS/JS/JSX/TSX/Vue SFC/Astro 等及版本 |
| 运行时 | Node 版本、浏览器 target、运行环境（H5/小程序/SSR） |
| 主框架 | Vue/React/Svelte/Solid/Angular/Next/Nuxt/uni-app/Taro 等 |
| 状态管理 | Pinia/Vuex/Redux/Zustand/Jotai/MobX |
| 路由 | vue-router / react-router / 框架内置 |
| UI 库 | Element Plus/Naive/Ant Design/uView/Vant/shadcn 等 |
| 图标 | iconify 集合、SVGR、字体图标 |
| 原子化 CSS | UnoCSS/Tailwind/Windi + presets/transformers |
| 预处理器 | Sass/Less/Stylus/PostCSS 插件 |
| 国际化 | vue-i18n / react-intl 等 |
| 数据请求 | axios / ofetch / uni.request / fetch wrapper |
| 业务 SDK | 项目特有依赖（如 `stock-sdk`、`echarts`） |
| 自动导入 | unplugin-auto-import、components 自动注册解析器 |
| Lint | ESLint 版本、配置预设、是否含 Vue/TS 插件、覆写规则 |
| Format | Prettier 是否启用 / Biome / 仅靠 ESLint |
| 包管理 | pnpm/yarn/npm/bun，workspace 信息 |
| 测试 | Vitest/Jest/Playwright/Cypress |
| 构建工具 | Vite/Webpack/Rspack/Turbopack 及关键插件 |
| CI/容器 | GitHub Actions、Docker |
| Git 忽略 | dist、node_modules 等 |

### Step 4 · 生成 AGENTS.md

在仓库**根目录**创建或覆盖 `AGENTS.md`（若已存在，先 `Read` 比较后再 `Write` 覆盖），结构必须包含以下 **11 个章节**，且每节都要给出可执行的、具体到本仓库的约束（避免空洞）：

1. **项目概览**：名称、定位、业务、入口文件（使用 `file:///` 绝对路径链接）
2. **技术栈表格**：列出"类别 / 选型 / 版本 / 备注"三列，必须含语言、框架、构建、CLI、状态、路由、UI、图标、原子化 CSS、预处理器、Lint、Format、包管理、自动导入、测试
3. **目录结构**：用代码块画出主要目录树，并写"新文件落位规则"（页面 / 组件 / store / utils / api / 静态资源去哪里）
4. **命令清单**：表格列出 `dev` / `build` / `lint` / `test` 等及其多端/多环境别名
5. **代码风格与编码约束**：脚本风格（setup / hooks）、别名导入、自动导入清单、命名规范、ESLint 覆写项、是否启用 Prettier
6. **样式与原子化 CSS**：默认写法、单位规则（rpx/px）、图标用法、SCSS 变量入口、可用的 transformer 指令（`@apply` / variant group）
7. **跨端 / 跨环境约束**（如项目跨端）：条件编译标记、禁用的浏览器 API、网络请求统一方式
8. **状态管理范式**：Store 模板代码、文件命名、禁用的插件
9. **类型与 IDE**：jsconfig/tsconfig 路径与 types，自动生成的 `.d.ts` 禁止手改清单
10. **提交与变更约束**：commit message 规范、敏感文件清单、gitignore 关键项
11. **AI Agent 行为约束**：必须包含
    - "先复用本文件事实，避免重复扫描"
    - 默认文件骨架顺序（如 `<script setup> + <template> + <style>`）
    - 新增依赖前先查 `package.json`
    - 禁止引入与既有体系冲突的工具链（按实际情况列出，如禁加 Prettier/TS/Tailwind）
    - 跨端 / 平台编译纪律
    - 默认包管理器命令前缀（pnpm/yarn/npm 之一）

### Step 5 · 写作质量要求

- **所有文件引用必须使用** `[basename](file:///绝对路径)` 格式，便于点击跳转。
- **链接文本禁止用反引号**包裹（会破坏渲染）。
- 技术栈版本号必须**与 `package.json` 完全一致**，不得估计。
- 不要编造未安装的依赖；如某能力未引入，应在"AI Agent 行为约束"中明确"禁止引入"。
- 中文项目使用中文写作；英文项目使用英文写作；保持与用户沟通语言一致。
- 不要写"待补充 / TBD / 占位"内容；如某节确实不适用，说明"本项目不涉及"。

### Step 6 · 完成后报告

向用户输出**简短摘要**：
1. 识别到的核心技术栈（语言 / 框架 / 构建 / 包管理 / Lint / 原子化 CSS / UI 库）
2. 生成的文件路径（点击链接）
3. 包含的章节标题列表
4. 任何需要用户后续确认的不确定点（如缺失 README、未发现 lint 配置等）

## 反模式（务必避免）

- ❌ 仅读 `package.json` 就生成约束（必须 Step 1-2 全部走完）
- ❌ 把"建议"写成约束（如"建议使用 TS"），约束必须基于**已存在的事实**
- ❌ 把 AGENTS.md 写成教程文档，应是**事实清单 + 强约束**
- ❌ 忽略 `.vscode/settings.json` 中的 `prettier.enable`、`editor.codeActionsOnSave` 等关键信号
- ❌ 把自动生成的 `.d.ts`（如 `components.d.ts`、`auto-imports.d.ts`）当作可手改源文件

## 输出长度参考

完整 AGENTS.md 建议 **200~400 行**，过短无法构成有效约束，过长会被截断且降低命中率。
