---
name: vue-engineering
description: Vue 前端工程化规范与实现/改造流程。Use when Codex is asked to create, migrate, audit, test, refactor, or modify Vue 3, Vite Vue, Nuxt, Vue Router, Pinia, Vue Test Utils, Vitest, Playwright E2E, component libraries, dashboards, design systems, .vue files, composables, route views, or Vue frontend monorepos. Always pair with vue-best-practices; use vue-router-best-practices for routing work, vue-testing-best-practices for tests, and web-design-guidelines for UI/UX/accessibility review.
---

# Vue 前端工程化

## 必须同时使用的 skills

- 任何 Vue、`.vue`、Nuxt、Pinia、Vite Vue、组件或 composable 任务：先使用 `vue-best-practices`。
- 任何路由、导航守卫、动态参数、route view、权限跳转任务：同时使用 `vue-router-best-practices`。
- 任何单元测试、组件测试、composable 测试、Pinia 测试、E2E 测试任务：同时使用 `vue-testing-best-practices`。
- 任何 UI 审核、可访问性、视觉设计、响应式、交互体验任务：同时使用 `web-design-guidelines`，并按该 skill 要求获取最新规则。

## 基础工程规则

- 全程使用中文回复、中文任务清单和中文代码注释。
- 默认使用 `pnpm`，所有安装、移除、脚本运行、一次性执行都使用 `pnpm`、`pnpm add`、`pnpm remove`、`pnpm dlx`、`pnpm exec`。不要使用 `npm` 或 `yarn`。
- Vue 前端项目默认采用 monorepo，根目录包含 `pnpm-workspace.yaml`、根 `package.json`、`apps/*`、`packages/*`。
- 依赖版本集中写在 `pnpm-workspace.yaml` 的 `catalog` 或 `catalogs` 中，各工作区 `package.json` 使用 `catalog:`、`catalog:<name>` 或 `workspace:*`。
- ESLint 默认使用 `@antfu/eslint-config`。
- 项目内部跨目录、跨包、跨 feature 引入必须使用 alias，不要新增 `../../../` 这类多层级相对引入。
- 所有交互元素必须有唯一且语义化的 `id`，方便自动化测试。

## 工作流程

1. 读取现有配置：`package.json`、`pnpm-workspace.yaml`、`tsconfig*.json`、`vite.config.*`、`nuxt.config.*`、`eslint.config.*`、`vitest.config.*`、`router` 目录和 `src` 结构。
2. 确认 Vue 架构：默认 Vue 3 + Composition API + `<script setup lang="ts">`；只有项目明确使用 Options API 时才延续 Options API。
3. 非简单任务先写组件边界：route view 负责组合，feature 组件负责界面，composable 负责状态和副作用。
4. 新增依赖先用 `pnpm add`，再把版本集中到 `pnpm-workspace.yaml` Catalogs。
5. 配置 alias 时同步 TypeScript、Vite/Nuxt、Vitest、ESLint 和包 `exports`。
6. 改动后优先运行 `pnpm lint`、`pnpm typecheck`、`pnpm test`、`pnpm build`；涉及端到端流程时运行 Playwright 或说明无法运行的原因。

## 引用文件

- Vue 组件、SFC、composable、状态和目录结构：读取 [references/vue-architecture.md](references/vue-architecture.md)。
- Vue Router 4、动态参数、导航守卫和 route lifecycle：读取 [references/vue-router.md](references/vue-router.md)。
- Vitest、Vue Test Utils、Pinia、异步组件和 E2E：读取 [references/vue-testing.md](references/vue-testing.md)。
- UI、可访问性、响应式和视觉审核：读取 [references/vue-ui-design.md](references/vue-ui-design.md)。
