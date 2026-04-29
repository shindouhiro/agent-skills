---
name: npm-publish
description: 为 TypeScript/JavaScript 库项目配置完整的 NPM 发布流程，包括正式发布（tag 触发）和预览发布（PR/push 触发）。当用户需要发布 NPM 包、配置 CI/CD 发布流水线、或设置 changelogithub 时使用。
metadata:
  author: Hikaru
  version: "2026.04.29"
---

# NPM 发布 Skill

为 TypeScript/JavaScript 库项目提供完整的 NPM 包发布方案，包含两个核心 workflow：

| Workflow | 触发条件 | 用途 |
|----------|----------|------|
| Release | 推送 `v*` tag | 正式发布到 NPM + 创建 GitHub Release |
| Publish Preview | PR 或 push 到 main | 发布预览版本，方便测试 |

## 前置条件

- 使用 **pnpm** 作为包管理器
- 项目的 `package.json` 中配置了 `build` 脚本
- GitHub 仓库已配置 `NPM_TOKEN` Secret（用于正式发布）

## 执行步骤

当此 skill 被调用时，按以下步骤执行：

### 1. 配置 package.json

确保 `package.json` 包含以下必要字段：

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

### 2. 安装 bumpp（版本管理工具）

```bash
pnpm add -D bumpp
```

[bumpp](https://github.com/antfu/bumpp) 提供交互式版本号选择，自动创建 git tag 并推送，触发 Release workflow。

### 3. 创建 GitHub Actions Workflows

将以下两个 workflow 文件放置到 `.github/workflows/` 目录：

#### 3a. 正式发布 — `.github/workflows/release.yml`

从模板复制：[release.yml](references/release.yml)

**流程说明**：
1. 推送 `v*` 格式的 tag 时触发
2. 安装依赖并构建项目
3. 使用 `pnpm publish` 发布到 NPM（`--no-git-checks` 跳过 git 状态检查，`--access public` 公开发布）
4. 使用 [changelogithub](https://github.com/antfu/changelogithub) 自动生成 changelog 并创建 GitHub Release

**所需 Secrets**：
- `NPM_TOKEN`：NPM 访问令牌（在 npmjs.com 的 Access Tokens 中生成）
- `GITHUB_TOKEN`：由 GitHub Actions 自动提供

#### 3b. 预览发布 — `.github/workflows/publish-preview.yml`

从模板复制：[publish-preview.yml](references/publish-preview.yml)

**流程说明**：
1. 每次 PR 或 push 到 main 时触发（排除 tag 推送）
2. 使用 [pkg-pr-new](https://github.com/stackblitz-labs/pkg.pr.new) 发布预览版本
3. 自动在 PR 中添加评论，提供可安装的预览包链接
4. 配置了并发控制，同一分支/PR 的多次推送会取消之前的运行

**无需额外 Secrets**，pkg-pr-new 使用 GitHub App 自动鉴权。

### 4. 配置 Monorepo（可选）

如果项目是 monorepo 结构，需调整 workflow：

**Release workflow**：将 `pnpm publish` 替换为针对各包的批量发布命令。

**Preview workflow**：在 `pkg-pr-new publish` 后指定需要发布的包路径：

```yaml
- run: pnpm dlx pkg-pr-new publish ./packages/core ./packages/utils --pnpm githubAction
```

---

## 发布流程

### 正式发布

```bash
# 1. 交互式选择版本号（patch/minor/major）
pnpm release

# bumpp 自动执行：
# - 更新 package.json version
# - git add & commit
# - 创建 git tag (v1.0.0)
# - git push --follow-tags
# → 触发 release.yml workflow → 发布到 NPM + 创建 GitHub Release
```

### 预览发布

```bash
# 推送代码或创建 PR 即自动触发
git push origin feat/my-feature
# → 触发 publish-preview.yml → PR 评论中出现预览包安装链接
# 安装方式：npm i https://pkg.pr.new/<package-name>@<commit-sha>
```

---

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

---

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Release Workflow | 正式发布到 NPM 的 GitHub Actions 模板 | [release.yml](references/release.yml) |
| Preview Workflow | 预览发布的 GitHub Actions 模板 | [publish-preview.yml](references/publish-preview.yml) |
| Monorepo Release | Monorepo 多包发布配置 | [monorepo-release.yml](references/monorepo-release.yml) |
