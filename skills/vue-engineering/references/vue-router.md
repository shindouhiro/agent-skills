# Vue Router 规则

## 基础

- 生产级 SPA 必须使用 Vue Router 4，不手写路由状态。
- route records 保持可读，复杂权限、标题、布局等信息放入 typed `meta`。
- route view 组件保持薄层，业务实现放入 feature 组件。
- 路由相关代码放在 `src/router` 或 feature 的 `routes` 中，再由主 router 聚合。

## 动态参数与生命周期

同一路由组件在 params/query 变化时通常不会重新挂载。需要显式监听：

```ts
const route = useRoute()

watch(
  () => route.params.id,
  async (id) => {
    if (typeof id === 'string')
      await loadDetail(id)
  },
  { immediate: true },
)
```

不要依赖 `onMounted` 处理同一路由参数变化。

## 导航守卫

- 守卫中的异步请求必须 `await`。
- 避免无条件 redirect，先判断目标路由，防止无限跳转。
- Vue Router 4 优先 return 路由位置或 `false`，不要新增旧式 `next()` 写法。
- `beforeRouteEnter` 中不能访问 `this`；Composition API 中优先使用 `onBeforeRouteLeave`、`onBeforeRouteUpdate`。

示例：

```ts
router.beforeEach(async (to) => {
  const auth = useAuthStore()

  if (!auth.ready)
    await auth.bootstrap()

  if (to.meta.requiresAuth && !auth.isLoggedIn)
    return { name: 'login', query: { redirect: to.fullPath } }

  if (to.name === 'login' && auth.isLoggedIn)
    return { name: 'home' }

  return true
})
```

## 清理

- route 组件中注册的事件监听、interval、订阅必须在 `onUnmounted` 清理。
- 对依赖 route param 的请求使用请求取消、序列号或最后一次请求胜出策略，避免旧响应覆盖新状态。
