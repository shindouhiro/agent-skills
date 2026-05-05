---
name: frontend-engineering
description: 前端工程化规范与初始化/改造流程。Use when Codex is asked to create, migrate, audit, or modify React, Vue, Angular, Next.js, Nuxt, Vite, Angular CLI, TypeScript frontend, frontend monorepo, component library, design system, SPA, SSR frontend, or admin dashboard projects; especially when requests mention pnpm, monorepo, PNPM Catalogs, @antfu/eslint-config, TailwindCSS, Radix UI, path aliases, dependency installation, lint/build/test scripts, or avoiding multi-level relative imports like ../../.
---

# 前端工程化

## 核心规则

- 全程使用中文回复、中文任务清单和中文代码注释。
- 默认使用 `pnpm`，所有安装、移除、脚本运行、一次性执行都使用 `pnpm`、`pnpm add`、`pnpm remove`、`pnpm dlx`、`pnpm exec`。不要使用 `npm` 或 `yarn`。
- 新建前端项目时默认采用 monorepo，根目录包含 `pnpm-workspace.yaml`、根 `package.json`、`apps/*`、`packages/*`。
- 依赖版本集中写在 `pnpm-workspace.yaml` 的 `catalog` 或 `catalogs` 中，各工作区 `package.json` 使用 `catalog:`、`catalog:<name>` 或 `workspace:*`。
- ESLint 默认使用 `@antfu/eslint-config`，不要同时引入 Prettier 作为第二套格式化规则，除非项目已有明确约束。
- 引入项目内部模块必须优先使用 alias，不要新增 `../../../` 这类多层级相对引入。单文件同目录或父一级的简单相对引入可保留，但跨模块、跨包、跨 feature 必须走 alias。
- 所有交互元素必须有唯一且语义化的 `id`，方便自动化测试。
- 新建页面必须包含语义化 HTML、响应式布局、标题层级、页面标题和 meta 描述。

## 工作流程

1. 先识别项目类型：React、Vue、Angular、Next.js、Nuxt、Vite、Angular CLI、组件库、设计系统或纯前端包工程。
2. 读取现有配置：`package.json`、`pnpm-workspace.yaml`、`tsconfig*.json`、`eslint.config.*`、框架配置文件、目录结构。
3. 如果缺少 pnpm workspace 或 Catalogs，优先补齐根工程化配置，再改业务代码。
4. 安装依赖时先用 `pnpm add`，随后检查版本是否被集中到 catalog；必要时手动把版本移入 `pnpm-workspace.yaml`，把工作区依赖改成 `catalog:`。
5. 配置 alias 时同时处理 TypeScript、构建工具、测试工具和框架配置，避免只让编辑器识别但运行时报错。
6. 改动后至少运行相关的 `pnpm lint`、`pnpm typecheck`、`pnpm test` 或 `pnpm build`；如果项目没有对应脚本，说明缺口并优先补基础脚本。

## 引用文件

- 处理 workspace、Catalogs、依赖、ESLint、alias 基线时读取 [references/monorepo.md](references/monorepo.md)。
- 针对 React、Vue、Angular 框架落地时读取 [references/frameworks.md](references/frameworks.md)。
