---
name: frontend-engineering
description: 前端工程化规范与初始化/改造流程。Use when asked to create, migrate, audit, or modify React, Vue, Angular, Next.js, Nuxt, Vite, Angular CLI, TypeScript frontend, frontend monorepo, component library, design system, SPA, SSR frontend, or admin dashboard projects; especially when requests mention pnpm, monorepo, PNPM Catalogs, @antfu/eslint-config, TailwindCSS, Radix UI, path aliases, dependency installation, lint/build/test scripts, or avoiding multi-level relative imports like ../../.
metadata:
  author: Hikaru
  version: "2026.05.07"
  source: pnpm docs, @antfu/eslint-config, Vite docs
---

# 前端工程化

> 通用前端工程化基线，适用于 React、Vue、Angular 及其元框架。框架特有规则由对应 skill（如 `vue-engineering`）扩展。

## Preferences

- 优先使用项目已有约定、工具链和目录结构。
- 全程使用中文回复、中文任务清单和中文代码注释。
- 默认使用 `pnpm`，不使用 `npm` 或 `yarn`。
- 新建前端项目时默认采用 monorepo 结构。
- ESLint 默认使用 `@antfu/eslint-config`，不引入 Prettier。
- 跨模块、跨包、跨 feature 引入必须走 alias，不新增 `../../../` 多层级相对引入。

## Workflow

1. 先识别项目类型：React、Vue、Angular、Next.js、Nuxt、Vite、Angular CLI、组件库、设计系统或纯前端包工程。
2. 读取现有配置：`package.json`、`pnpm-workspace.yaml`、`tsconfig*.json`、`eslint.config.*`、框架配置文件、目录结构。
3. 如果缺少 pnpm workspace 或 Catalogs，优先补齐根工程化配置，再改业务代码。
4. 安装依赖时先用 `pnpm add`，随后检查版本是否被集中到 catalog；必要时手动把版本移入 `pnpm-workspace.yaml`。
5. 配置 alias 时同时处理 TypeScript、构建工具、测试工具和框架配置。
6. 改动后至少运行相关的 `pnpm lint`、`pnpm typecheck`、`pnpm test` 或 `pnpm build`。

## Core Rules

- 依赖版本集中写在 `pnpm-workspace.yaml` 的 `catalog` / `catalogs` 中，工作区使用 `catalog:`、`catalog:<name>` 或 `workspace:*`。
- 所有交互元素必须有唯一且语义化的 `id`，方便自动化测试。
- 新建页面必须包含语义化 HTML、响应式布局、标题层级、页面标题和 meta 描述。

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Monorepo 基线 | workspace、Catalogs、依赖、ESLint、alias 配置 | [monorepo.md](references/monorepo.md) |
| 框架落地 | React、Vue、Angular 具体框架的落地规则 | [frameworks.md](references/frameworks.md) |

## Quick Reference

### 常用命令

```bash
pnpm add <pkg> --filter <workspace>    # 安装到指定工作区
pnpm add -D <pkg> -w                   # 安装到根 devDependencies
pnpm remove <pkg> --filter <workspace> # 移除
pnpm exec <cmd>                        # 在当前工作区运行
pnpm dlx <cmd>                         # 一次性执行
pnpm -r build                          # 递归构建所有包
```

### alias 优先级

```ts
// ✅ 推荐
import { Button } from '@repo/ui/button'
import { formatDate } from '@/shared/date'

// ❌ 避免
import { Button } from '../../../packages/ui/src/button'
```

## Validation Checklist

- `name` 使用小写短横线命名，且与目录名一致。
- `description` 明确写出触发场景、相关工具或文件类型。
- monorepo 根目录包含 `pnpm-workspace.yaml` 且 Catalogs 已配置。
- alias 在 TypeScript、构建工具、测试工具中保持一致。
- 改动后运行 lint/typecheck/test/build 验证。
