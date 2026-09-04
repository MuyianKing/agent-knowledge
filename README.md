# Agent Knowledge

面向 AI Coding Agent 的知识库与技能仓库，集中维护可复用的开发规范、技术文档和工作流定义。

## 目录

- [Skills](#skills)：可复用的 Agent 工作流和实现规范
- [文档](#文档)：技术经验和方案记录

## Skills

### 页面与组件开发

- [curd-page](skills/curd-page/SKILL.md)
  - 根据接口文档和字段要求，实现查询、列表、新增、编辑、删除等完整前端 CURD 页面。
  - 覆盖字段整理、列表自定义、表单自定义、字段联动、接口服务和完成检查。
- [html-to-vue](skills/html-to-vue/SKILL.md)
  - 将静态 HTML 页面转换为符合目标项目规范的 Vue 页面或组件。
  - 覆盖项目规则读取、组件拆分、数据逻辑分层、样式还原和转换自检。

### 项目规范

- [generate-project-constraints](skills/generate-project-constraints/SKILL.md)
  - 扫描目标项目并生成根目录 `AGENTS.md`，为 AI Coding Agent 提供稳定的项目上下文。
  - 覆盖项目结构、技术栈、文件落位、编码规范、UI 样式、请求状态和验证规则。
- [agent-common](skills/generate-project-constraints/AGENT_COMMON.md)
  - 跨项目通用的 Agent 约束模板。
  - 包含修改原则、组件设计、UI 与样式、验证与 Git 等通用规则。

### Skills 参考资料

- [curd-conventions](skills/curd-page/references/curd-conventions.md)
  - CURD 页面实现的补充参考，记录项目上下文读取、字段清单、页面职责、接口服务和完成检查等内容。

## 文档

- [单例浮层模式组件创建指南](docs/单例浮层模式组件创建指南.md)
  - 介绍多个选择器实例共享单例浮层的组件设计模式。
  - 覆盖 Teleport、全局状态管理、动态定位、竞态处理、生命周期清理和大数据量场景优化。
  <br />

