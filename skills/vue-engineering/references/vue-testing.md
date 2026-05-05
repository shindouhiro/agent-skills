# Vue 测试规则

## 默认工具

- 单元测试和组件测试使用 Vitest。
- Vue 组件测试使用 Vue Test Utils。
- 端到端测试优先使用 Playwright。
- 测试依赖版本写入 `pnpm-workspace.yaml` Catalogs，工作区使用 `catalog:`。

## 测试策略

- 优先黑盒测试用户可见行为，不测试组件内部实现细节。
- 避免只写 snapshot；snapshot 只能作为补充。
- 组件 props、emits、插槽、可访问名称、表单交互、异步状态都要通过行为断言。
- composable 如果依赖生命周期、provide/inject 或插件，需要测试 wrapper。
- Pinia 测试使用测试 pinia 或真实 store 初始化，避免缺失 injection。

## 异步

异步交互后等待 DOM 更新：

```ts
await wrapper.find('#save-button').trigger('click')
await flushPromises()
await nextTick()
```

规则：

- 网络、定时器、router navigation、Suspense、async setup 都要显式等待。
- 使用 fake timers 时负责恢复真实 timers。
- 不用固定 `setTimeout` 等待测试。

## Vitest 配置

Vue 组件测试默认使用 `jsdom` 或需要真实浏览器行为时使用 browser runner。

```ts
import vue from '@vitejs/plugin-vue'
import { defineConfig } from 'vitest/config'

export default defineConfig({
  plugins: [vue()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: ['./src/test/setup.ts'],
  },
})
```

## E2E

- 关键用户路径使用 Playwright。
- 测试选择器优先使用语义化 role/name；项目要求自动化定位时交互元素也必须有唯一 `id`。
- E2E 覆盖登录、导航、表单提交、错误状态、权限跳转等真实路径。
