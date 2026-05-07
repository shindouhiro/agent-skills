---
name: vue-engineering
description: Vue 前端工程化规范与实现/改造流程。Use when asked to create, migrate, audit, test, refactor, or modify Vue 3, Vite Vue, Nuxt, Vue Router, Pinia, Vue Test Utils, Vitest, Playwright E2E, component libraries, dashboards, design systems, .vue files, composables, route views, or Vue frontend monorepos. Always pair with vue-best-practices; use vue-router-best-practices for routing work, vue-testing-best-practices for tests, and web-design-guidelines for UI/UX/accessibility review.
metadata:
  author: Hikaru
  version: "2026.05.07"
  source: Vue 3 docs, Vite docs, VueUse docs
---

# Vue 前端工程化

> 基于 `frontend-engineering` 通用规则，叠加 Vue 生态特有的架构、组件和工具约定。

## Preferences

- **前置依赖**：本 skill 继承 `frontend-engineering` 的所有基础规则（pnpm、monorepo、Catalogs、alias、ESLint）。不在此重复，冲突时以本 skill 为准。
- 默认使用 Vue 3 + TypeScript + Composition API + `<script setup lang="ts">`。
- 只有项目明确使用 Options API 时才延续 Options API。
- 状态优先本地 `ref`/`reactive` + `computed`，跨 feature 或应用级状态再使用 Pinia。

## 必须同时使用的 Skills

| 任务类型 | 配对 Skill |
|----------|-----------|
| 任何 Vue、`.vue`、Nuxt、Pinia、composable 任务 | `vue-best-practices` |
| 路由、导航守卫、动态参数、route view | `vue-router-best-practices` |
| 单元测试、组件测试、E2E 测试 | `vue-testing-best-practices` |
| UI 审核、可访问性、视觉设计 | `web-design-guidelines` |

## Workflow

1. 读取现有配置：`package.json`、`pnpm-workspace.yaml`、`tsconfig*.json`、`vite.config.*`、`nuxt.config.*`、`eslint.config.*`、`vitest.config.*`、`router` 目录和 `src` 结构。
2. 确认 Vue 架构：默认 Vue 3 + Composition API + `<script setup lang="ts">`。
3. 非简单任务先定义组件边界：route view 负责组合，feature 组件负责界面，composable 负责状态和副作用。
4. 新增依赖先用 `pnpm add`，再把版本集中到 Catalogs。
5. 配置 alias 时同步 TypeScript、Vite/Nuxt、Vitest、ESLint 和包 `exports`。
6. 改动后优先运行 `pnpm lint`、`pnpm typecheck`、`pnpm test`、`pnpm build`；涉及端到端流程时运行 Playwright 或说明无法运行的原因。

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Vue 架构 | SFC 结构、组件拆分、目录建议和 alias 规则 | [vue-architecture.md](references/vue-architecture.md) |
| Vue Router | 动态参数、导航守卫和 route lifecycle | [vue-router.md](references/vue-router.md) |
| Vue 测试 | Vitest、Vue Test Utils、Pinia 和 E2E | [vue-testing.md](references/vue-testing.md) |
| Vue UI 设计 | UI 实现、响应式、可访问性和视觉约束 | [vue-ui-design.md](references/vue-ui-design.md) |

## Validation Checklist

- 继承 `frontend-engineering` 基础规则，不重复定义 pnpm/monorepo/alias/ESLint 规则。
- Vue 组件使用 `<script setup lang="ts">` + Composition API。
- 配对 skill 按任务类型正确引用。
- alias 同步覆盖 TypeScript、Vite/Nuxt、Vitest 和 ESLint。
- 改动后运行 lint、typecheck、test、build 验证。
