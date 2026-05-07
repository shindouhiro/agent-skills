# NPM 发布配置指南

## package.json 必要字段

确保 `package.json` 包含以下字段：

```json
{
  "name": "<package-name>",
  "version": "0.0.0",
  "type": "module",
  "files": ["dist"],
  "scripts": {
    "build": "tsdown",
    "prepack": "pnpm build",
    "release": "bumpp -r"
  }
}
```

> **注意**：`exports` 字段建议由 tsdown 的 `exports: true` 自动管理。

## 安装 bumpp

```bash
pnpm add -D bumpp
```

[bumpp](https://github.com/antfu/bumpp) 提供交互式版本号选择，自动创建 git tag 并推送，触发 Release workflow。

## Monorepo 调整

如果项目是 monorepo 结构，需调整 workflow：

**Release workflow**：将 `pnpm publish` 替换为 `pnpm --recursive publish`。

**Preview workflow**：在 `pkg-pr-new publish` 后指定需要发布的包路径：

```yaml
- run: pnpm dlx pkg-pr-new publish ./packages/core ./packages/utils --pnpm githubAction
```

## Commit 规范

使用 [Conventional Commits](https://www.conventionalcommits.org/) 规范，changelogithub 会自动根据 commit message 分类生成 changelog：

| Prefix | 类别 | 示例 |
|--------|------|------|
| `feat:` | 新功能 | `feat: add dark mode support` |
| `fix:` | 修复 | `fix: resolve SSR hydration issue` |
| `perf:` | 性能 | `perf: optimize bundle size` |
| `refactor:` | 重构 | `refactor: extract shared utils` |
| `chore:` | 杂项 | `chore: update dependencies` |
| `docs:` | 文档 | `docs: update API reference` |

**Breaking Changes** 使用 `!` 后缀：`feat!: drop Node 16 support`

## 所需 Secrets

| Secret | 用途 | 来源 |
|--------|------|------|
| `NPM_TOKEN` | NPM 访问令牌 | npmjs.com → Access Tokens（Automation 类型） |
| `GITHUB_TOKEN` | GitHub Release | GitHub Actions 自动提供 |
