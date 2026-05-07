---
name: gh-quick-publish
description: 使用 GitHub CLI（gh）快速创建 GitHub 仓库、初始化本地 Git、提交代码、推送分支并按需创建 Pull Request。适用于用户要求"创建项目并提交到 GitHub""用 gh-cli 发布项目""初始化仓库并推送""创建 GitHub repo""提交当前项目到 GitHub""开 PR / draft PR""配置 Docker CI"等本地项目发布与仓库创建任务。
metadata:
  author: Hikaru
  version: "2026.05.07"
  source: gh-cli docs, GitHub Actions
---

# GH Quick Publish

> 使用 `gh` 作为 GitHub 操作入口，优先完成从本地目录到远端仓库的最短可靠路径。

## Preferences

- 保持操作可审计：先检查状态，再创建或关联仓库，再提交、推送、按需建 PR。
- 不使用 `git reset --hard`、`git clean -fd`、`git checkout -- <file>` 等破坏性命令，除非用户明确要求。
- 不提交密钥、token、`.env`、私有证书、大型构建产物或依赖目录。
- 已有仓库优先复用；不创建重复 GitHub 仓库。
- 默认公开仓库，除非用户明确要求 private。

## Workflow

1. **确认工具与身份**：验证 `gh`、`git` 可用，`gh auth status` 已登录。
2. **读取项目状态**：检查是否为 Git 仓库，检查 remote 和暂存区。
3. **补齐发布前基础文件**：确认 `.gitignore` 覆盖依赖目录、构建产物、环境文件；如有检查脚本优先运行。
4. **创建或关联 GitHub 仓库**：用户未指定可见性时默认公开。
5. **提交代码**：确认提交范围后执行 `git add` + `git commit`，遵循 Conventional Commits。
6. **推送分支**：初始发布推 `main`，功能交付推 `codex/<short-task-name>` 分支。
7. **按需创建 PR**：仅在用户要求或功能分支需要 review 时创建，默认 draft PR。
8. **按需配置 Docker CI**（可选）：从模板复制 workflow 并替换占位符。

## Decision Rules

- 仓库名优先来自用户；否则从目录名生成小写 kebab-case 名称。
- owner 优先来自用户；否则使用 `gh api user --jq .login` 获取。
- 默认分支优先使用现有分支；新仓库默认 `main`。
- 只在确认提交范围后使用 `git add .`。
- 提交信息描述用户可见结果，如 `chore: initialize project`、`feat: add landing page`。

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| 命令片段 | gh、git 常用命令模板与完整操作步骤 | [commands.md](references/commands.md) |
| Docker CI | Docker 构建与推送的 GitHub Actions 模板 | [docker-build.yml](references/docker-build.yml) |

## 最终回复

完成后报告：

- GitHub 仓库 URL
- 推送的分支
- 提交哈希
- PR URL（如果创建）
- 已运行的验证命令
- 未完成或需要用户处理的事项

## Validation Checklist

- `name` 使用小写短横线命名，且与目录名一致。
- `description` 明确写出触发场景和关键短语。
- SKILL.md 保持精简，命令模板通过 References 按需读取。
- 不包含密钥、token 等敏感信息模板。
- 示例命令使用 `gh` CLI 而非直接 API 调用。
