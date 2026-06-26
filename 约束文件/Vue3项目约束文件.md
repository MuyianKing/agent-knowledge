---
tags:
  - 约束
  - 规范
  - 项目配置
  - Vue3
created: 2026-06-26
---

# 项目约束 (Project Rules)

## 一、项目概述

- **项目名称**：mu-material（基础模板/组件库工程）
- **类型**：Monorepo（pnpm workspace）
- **包管理器**：pnpm（10.32.1，强制使用，禁止 npm/yarn）
- **Node 版本**：22.22.0（通过 volta 锁定）
- **模块规范**：ESM（`"type": "module"`），统一使用 `import/export`，禁止使用 CommonJS（`require`）

## 二、目录结构约束

```
basic-template/
├── app/                # 应用层（最终运行的项目）
│   └── demo/           # 示例 / 业务应用
├── packages/           # 内部可复用包
│   ├── directives/     # Vue 自定义指令
│   ├── hooks/          # 组合式 hooks
│   ├── smartui/        # 业务组件（form / query / table）
│   ├── theme-chalk/    # 组件样式（CSS）
│   └── ui/             # 基础 UI 组件库
├── eslint.config.js    # ESLint 扁平配置
├── commitlint.config.js
├── pnpm-workspace.yaml
└── package.json
```

约束：

- 新增内部包必须放在 `packages/` 下，且包名以 `@mu/` 为前缀。
- 新增应用必须放在 `app/` 下。
- `packages/ui` 下每个组件必须遵循目录约定：`components/<kebab-name>/{Index.vue, index.js, style/index.js}`。
- `packages/theme-chalk` 仅存放 CSS，不得引入 JS 逻辑。
- 静态资源放在对应包/应用的 `src/assets/` 下，禁止放在 `public/` 之外的根目录。

## 三、技术栈约束

- **框架**：Vue 3.5+（Composition API + `<script setup>` 优先）
- **构建工具**：Rsbuild（禁止在本仓库引入 Vite/Webpack 配置）
- **路由**：vue-router 5.x
- **状态管理**：Pinia 3.x（持久化使用 `pinia-plugin-persistedstate`）
- **UI 库**：Element Plus 2.x（按需引入，使用 `unplugin-element-plus`）
- **HTTP**：axios（统一封装在 `app/demo/src/utils/request.js`，禁止直接在视图中调用 `axios`）
- **工具库**：lodash-es、dayjs、@vueuse/core、@muyianking/utils
- **图标**：`unplugin-icons` + `@iconify/json`
- **样式**：Tailwind CSS（仅在 `app/demo` 中）+ theme-chalk CSS

约束：

- 新增第三方依赖前需评估是否与已有库重复（如不要再引入 moment、underscore）。
- 不允许引入 jQuery、Vue 2 相关包。
- 时间处理统一使用 dayjs，工具函数优先使用 lodash-es 与 @muyianking/utils。

## 四、代码风格

- **ESLint**：基于 `@antfu/eslint-config` + `@muyianking/config`，必须通过 lint 校验。
- **格式化**：由 ESLint 的 `formatters: true` 接管，不另外引入 Prettier。
- **命名规范**：
  - 文件：
    - 组件文件首字母大写（如 `Index.vue`、`Header.vue`）。
    - 普通 JS 文件使用 kebab-case（如 `use-list.js`、`request.class.js`）。
    - **Pinia store 文件为例外**：与导出的 composable 同名，使用 camelCase（如 `useUserStore.js`、`useAppStore.js`），便于 `unplugin-auto-import` 按 `dirs: ['./src/pinia/modules']` 自动按文件名导入。
    - **`packages/hooks` 内的 hook 文件**：可使用 kebab-case（如 `use-list/`、`use-request.js`），导出函数仍为 `useXxx`。
  - 组件：PascalCase。
  - Hook：函数名以 `use` 开头，camelCase（如 `useListRequest`、`useScreen`）。
  - 常量：UPPER_SNAKE_CASE，统一放在 `utils/constant.js`。
  - Pinia store：
    - 函数名以 `useXxxStore` 形式命名（如 `useUserStore`）。
    - 必须存放在 `pinia/modules/` 目录下，由根 `pinia/index.js` 统一注册。
    - 文件名与导出函数名保持一致（camelCase），一个文件只导出一个 store。
    - 需要持久化的 store 使用 `pinia-plugin-persistedstate`，并显式声明 `persist` 配置。
- **缩进**：2 空格；不使用分号（遵循 antfu 风格）；字符串使用单引号。
- **导入顺序**：第三方包 → 内部包（`@mu/*`） → 相对路径，由 ESLint 自动整理。
- 禁止出现 `console.log`（调试信息提交前必须清理），允许 `console.warn`、`console.error`。
- 禁止提交注释掉的死代码。

## 五、Vue 编码约束

- 单文件组件统一使用 `<script setup>`，避免 Options API（除特殊场景）。
- Props 必须显式声明类型与默认值；事件必须通过 `defineEmits` 声明。
- 避免在模板中写复杂表达式，超过两层逻辑应抽离为 `computed`。
- `:style` 中禁止使用对象字面量定义动态样式，必须提取为计算属性。
- 禁止使用 scss、less 等 CSS 预处理器，统一使用原生 CSS
- 没有明确的指令不能修改 packages 下的任何文件，也不能创建任何文件

## 六、Git 与提交规范

- 提交信息遵循 **Conventional Commits**，由 commitlint 校验。
- 允许的 type：`feat`、`fix`、`docs`、`style`、`refactor`、`perf`、`test`、`build`、`ci`、`revert`、`chore`。
- 提交前会通过 husky 触发 `pre-commit`（lint）与 `commit-msg`（commitlint），不得使用 `--no-verify` 跳过。
- 分支约定：`main` 为主干，`feat/*`、`fix/*`、`chore/*` 为功能分支。
- 禁止直接向 `main` 强推（`push --force`）。

## 七、依赖管理约束

- 仅使用 `pnpm` 安装依赖；根目录运行 `pnpm i` 完成 workspace 安装。
- 内部包之间引用使用 `workspace:*` 协议。
- 升级依赖统一通过 `pnpm --filter <pkg> update` 或 `npm-check-updates`。
- `resolutions` 中的版本锁定不得随意改动（如 `bin-wrapper`）。

## 八、环境与运行命令

- 开发：`pnpm --filter muyian-rsbuild-web-demo dev`
- 构建：`pnpm --filter muyian-rsbuild-web-demo build`
- 构建分析：`pnpm --filter muyian-rsbuild-web-demo build:analyze`
- 预览：`pnpm --filter muyian-rsbuild-web-demo preview`
- Lint：`pnpm exec eslint .`（提交前必须执行）

## 九、安全与提交前检查

- 禁止提交 `.env*` 中的真实密钥，敏感信息必须通过环境变量注入。
- 禁止提交 `node_modules`、`dist`、`.rsbuild`、IDE 个人配置（除 `.vscode` 共享配置）。
- 提交前确保：
  1. ESLint 通过。
  2. 项目能正常 `dev` 启动。
  3. 不引入未使用的依赖、变量、文件。

## 十、其他约定

- 文档（README/TODO）按需维护在对应包目录下，不在根目录堆放散乱说明。
- 新增功能优先复用 `@mu/ui`、`@mu/smartui`、`@mu/hooks`、`@mu/directives`，避免重复造轮子。
- 涉及破坏性变更需在 commit 中标注 `BREAKING CHANGE` 并同步更新对应包 README。