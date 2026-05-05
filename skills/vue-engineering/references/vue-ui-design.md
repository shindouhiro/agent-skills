# Vue UI 与设计规则

## 使用 web-design-guidelines

进行 UI 审核、可访问性检查、响应式检查或视觉体验评估时，先按 `web-design-guidelines` skill 获取最新规则：

```text
https://raw.githubusercontent.com/vercel-labs/web-interface-guidelines/main/command.md
```

然后再读取目标 Vue 文件、样式文件和页面入口，输出具体文件与行号上的问题。

## Vue UI 实现

- 不要把完整应用做成落地页；用户要求应用、工具、后台、游戏时，首屏应是可用体验。
- 管理后台、SaaS、CRM、运营工具应保持密集、清晰、克制，避免营销式 hero 和大装饰卡片。
- 使用现代字体和稳定排版，不使用浏览器默认字体。
- TailwindCSS 是默认样式方案；复杂组件优先封装到共享 UI 包。
- 按钮、输入框、菜单、弹窗、标签页、切换器等交互元素必须有唯一且描述性的 `id`。
- 文本不能溢出按钮、导航、卡片、工具栏或移动端容器。
- 页面必须包含语义化结构、合理标题层级、页面 title 和 meta description。

## 响应式与可访问性

- 移动端、平板、桌面都要验证布局。
- 表单控件必须有关联 label 或可访问名称。
- 图标按钮必须有 `aria-label` 或 tooltip。
- 弹窗、菜单、抽屉要处理焦点、键盘关闭和返回焦点。
- 颜色对比度、hover/focus/disabled/loading/error 状态都要完整。

## 视觉约束

- 避免单一色相铺满整页；使用明确的主色、中性色、状态色和层级。
- 卡片只用于重复项、弹窗或真实 framed tool，不把页面 section 套成卡片。
- 不使用装饰性渐变球、模糊光斑或无意义 SVG 背景。
- 动画服务于状态变化和交互反馈，不制造持续干扰。
