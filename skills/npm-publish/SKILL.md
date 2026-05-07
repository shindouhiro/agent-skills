---
name: npm-publish
description: 为 TypeScript/JavaScript 库项目配置完整的 NPM 发布流程，包括正式发布（tag 触发）和预览发布（PR/push 触发）。Use when asked to publish NPM packages, configure CI/CD release pipelines, set up changelogithub, bumpp, pkg-pr-new, or GitHub Actions for library publishing.
metadata:
  author: Hikaru
  version: "2026.05.07"
  source: bumpp, changelogithub, pkg-pr-new
---

# NPM 发布

> 为 TypeScript/JavaScript 库提供完整的 NPM 包发布方案，覆盖正式发布和预览发布。

## Preferences

- 使用 **pnpm** 作为包管理器。
- 版本管理使用 [bumpp](https://github.com/antfu/bumpp)，交互式选择版本号。
- Changelog 使用 [changelogithub](https://github.com/antfu/changelogithub) 自动生成。
- 预览发布使用 [pkg-pr-new](https://github.com/stackblitz-labs/pkg.pr.new)。
- Commit 遵循 Conventional Commits 规范。

## Workflow

1. 确认 `package.json` 包含必要字段（name、version、files、build/release 脚本）。
2. 安装 `bumpp` 作为版本管理工具。
3. 创建 Release workflow（`.github/workflows/release.yml`）：tag 触发 → 构建 → 发布到 NPM → 创建 GitHub Release。
4. 创建 Preview workflow（`.github/workflows/publish-preview.yml`）：PR/push 触发 → 发布预览版本。
5. 确保 GitHub 仓库已配置 `NPM_TOKEN` Secret。
6. Monorepo 项目调整为批量发布。

## Workflows 概览

| Workflow | 触发条件 | 用途 |
|----------|----------|------|
| Release | 推送 `v*` tag | 正式发布到 NPM + 创建 GitHub Release |
| Publish Preview | PR 或 push 到 main | 发布预览版本，方便测试 |

## 发布流程

### 正式发布

```bash
pnpm release
# bumpp 自动：更新 version → git commit → 创建 tag → push
# → 触发 release.yml → 发布到 NPM + GitHub Release
```

### 预览发布

```bash
git push origin feat/my-feature
# → 触发 publish-preview.yml → PR 评论中出现预览包安装链接
```

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| 配置指南 | package.json 配置、bumpp 安装、Commit 规范、Secrets | [setup-guide.md](references/setup-guide.md) |
| Release Workflow | 正式发布到 NPM 的 GitHub Actions 模板 | [release.yml](references/release.yml) |
| Preview Workflow | 预览发布的 GitHub Actions 模板 | [publish-preview.yml](references/publish-preview.yml) |
| Monorepo Release | Monorepo 多包发布配置 | [monorepo-release.yml](references/monorepo-release.yml) |

## Validation Checklist

- `package.json` 包含 `name`、`version`、`files`、`build` 和 `release` 脚本。
- bumpp 已安装为 devDependency。
- GitHub 仓库已配置 `NPM_TOKEN` Secret。
- Release workflow 使用 `pnpm publish --no-git-checks --access public`。
- Preview workflow 配置了并发控制（同一分支取消之前的运行）。
- Commit message 遵循 Conventional Commits 规范。
