# Vue 架构规则

## 默认技术选择

- Vue 3 + TypeScript + Composition API + `<script setup lang="ts">`。
- Vite Vue 使用 `@vitejs/plugin-vue`；Nuxt 使用 Nuxt 原生约定和 auto imports。
- 类型检查使用 `vue-tsc`。
- 状态优先本地 `ref`/`reactive` + `computed`，跨 feature 或应用级状态再使用 Pinia。

## SFC 结构

SFC 顺序固定：

```vue
<script setup lang="ts">
</script>

<template>
</template>

<style scoped>
</style>
```

规则：

- 模板保持声明式，复杂分支、派生数据、过滤和排序放到 `<script setup>` 的 `computed`。
- watcher 只用于副作用，不用于能被 `computed` 表达的派生状态。
- `v-html` 只在可信内容或已清洗内容上使用。
- `v-for` 必须有稳定 `key`，不要使用数组 index 作为可变列表 key。
- props、emits、provide/inject 必须显式类型化。

## 组件拆分

非简单功能先定义组件边界：

- route view：只负责布局、数据装配、feature 组合。
- feature container：负责 feature 状态、请求、副作用和子组件协调。
- presentational component：只接收 props、发出 emits，不直接依赖全局状态。
- composable：封装可复用逻辑、状态机、请求、订阅和浏览器副作用。

触发拆分的信号：

- 一个组件同时承担数据编排和多个 UI 区块。
- 模板出现 3 个以上独立区域，例如筛选、表单、列表、页脚。
- 逻辑可复用、可测试或包含副作用。
- route view 中出现完整业务实现。

## 目录建议

```text
src/
├── app/
├── router/
├── features/
│   └── orders/
│       ├── components/
│       ├── composables/
│       ├── routes/
│       └── types.ts
├── shared/
│   ├── components/
│   ├── composables/
│   └── utils/
└── styles/
```

## Alias

优先使用：

```ts
import OrderList from '@/features/orders/components/OrderList.vue'
import { useOrders } from '@/features/orders/composables/useOrders'
import { Button } from '@repo/ui/button'
```

避免新增：

```ts
import OrderList from '../../../features/orders/components/OrderList.vue'
```
