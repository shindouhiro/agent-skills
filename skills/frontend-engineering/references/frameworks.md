# React、Vue、Angular 落地规则

## React

适用触发：React、Next.js、Vite React、Radix UI、组件库、SPA、管理后台。

- Vite React 项目使用 `@vitejs/plugin-react`，依赖版本来自 `catalogs.react`。
- UI 基础组件优先放在 `packages/ui`，业务组件放在应用内的 `src/features/*`。
- Radix UI 组件安装到具体使用的 workspace，版本进入默认 catalog 或 `catalogs.react`。
- `vite.config.ts` 需要与 `tsconfig.base.json` 的 alias 保持一致。

```ts
import path from 'node:path'
import react from '@vitejs/plugin-react'
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src'),
      '@repo/ui': path.resolve(__dirname, '../../packages/ui/src'),
      '@repo/shared': path.resolve(__dirname, '../../packages/shared/src'),
    },
  },
})
```

## Vue

适用触发：Vue、Vue 3、Vite Vue、Nuxt、Pinia、Vue Router。

- 默认使用 Vue 3、`<script setup lang="ts">` 和 Composition API。
- Vue 类型检查使用 `vue-tsc`，版本来自 `catalogs.vue`。
- 跨包 UI 与 composables 通过 `@repo/ui/*`、`@repo/shared/*` 暴露，不在应用中引用包内部相对路径。
- Nuxt 项目优先使用 Nuxt 的 alias 与 auto imports，仍需保证 TypeScript paths 一致。

```ts
import path from 'node:path'
import vue from '@vitejs/plugin-vue'
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src'),
      '@repo/ui': path.resolve(__dirname, '../../packages/ui/src'),
      '@repo/shared': path.resolve(__dirname, '../../packages/shared/src'),
    },
  },
})
```

## Angular

适用触发：Angular、Angular CLI、Angular workspace、Angular library、Nx Angular。

- Angular CLI 依赖统一放入 `catalogs.angular`。
- `tsconfig.base.json` 或根 `tsconfig.json` 管理 paths，库通过 `@repo/<name>` 暴露。
- Angular library 优先放在 `packages/*`，应用放在 `apps/*`。
- 修改 paths 后同步检查 `angular.json`、测试配置和构建配置，确保 builder 能解析 alias。

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@app/*": ["apps/web/src/app/*"],
      "@repo/ui": ["packages/ui/src/public-api.ts"],
      "@repo/shared/*": ["packages/shared/src/*"]
    }
  }
}
```

## 框架共同要求

- 页面入口设置 title、meta description 和合理标题层级。
- 表单、按钮、菜单、弹窗、标签页等交互元素必须有唯一且描述性的 `id`。
- 样式默认使用 TailwindCSS；需要复杂交互时使用框架生态内成熟方案，不手写脆弱状态机。
- 移动端、平板、桌面都要验证布局，不让文字溢出按钮、卡片、导航或工具栏。
