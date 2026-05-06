---
name: gh-quick-publish
description: 使用 GitHub CLI（gh）快速创建 GitHub 仓库、初始化本地 Git、提交代码、推送分支并按需创建 Pull Request。适用于用户要求“创建项目并提交到 GitHub”“用 gh-cli 发布项目”“初始化仓库并推送”“创建 GitHub repo”“提交当前项目到 GitHub”“开 PR / draft PR”等本地项目发布与仓库创建任务。
---

# GH Quick Publish

## Overview

使用 `gh` 作为 GitHub 操作入口，优先完成从本地目录到远端仓库的最短可靠路径。保持操作可审计：先检查状态，再创建或关联仓库，再提交、推送、按需建 PR。

## 工作流

1. 确认工具与身份：

```bash
gh --version
gh auth status
git --version
```

如果 `gh auth status` 失败，提示用户先执行 `gh auth login`。不要替用户处理、输出或保存 token。

2. 读取项目状态：

```bash
pwd
git status --short --branch
git remote -v
```

若目录不是 Git 仓库，使用 `git init -b main` 初始化。若已有用户改动，先说明将提交哪些文件；不要重置、覆盖或丢弃未明确属于本次任务的改动。

3. 补齐发布前基础文件：

- 确认 `.gitignore` 覆盖依赖目录、构建产物、环境文件和系统文件，例如 `node_modules/`、`dist/`、`.env*`、`.DS_Store`。
- 若项目有测试或构建脚本，优先运行最相关的检查，例如 `pnpm lint`、`pnpm test`、`pnpm build`、`uv run pytest`。
- 若检查因环境缺失失败，在最终结果中明确说明失败命令与原因。

4. 创建或关联 GitHub 仓库：

如果用户没有指定可见性，默认创建公开仓库，除非当前组织规则或用户上下文明确要求私有。

新建并推送当前目录：

```bash
gh repo create <owner>/<repo> --public --source=. --remote=origin --push
```

仅创建远端仓库，稍后手动推送：

```bash
gh repo create <owner>/<repo> --public
git remote add origin git@github.com:<owner>/<repo>.git
git push -u origin main
```

若 `origin` 已存在，先确认它指向的仓库是否就是目标仓库。不要直接覆盖 remote；需要变更时使用 `git remote set-url origin <url>` 并说明原因。

5. 提交代码：

```bash
git status --short
git add <明确文件路径>
git status --short
git commit -m "<简洁中文或英文提交信息>"
```

只在确认提交范围后使用 `git add .`。提交信息描述用户可见结果，例如 `chore: initialize project`、`feat: add landing page`。

6. 推送分支：

```bash
git branch --show-current
git push -u origin <branch>
```

若是初始项目发布，通常直接推送 `main`。若是在现有仓库中交付新功能，创建 `codex/<short-task-name>` 分支后推送。

7. 按需创建 PR：

仅当用户要求 PR、当前工作在功能分支，或项目流程明显需要 review 时创建 PR。默认用 draft PR，除非用户明确要求 ready PR。

```bash
gh pr create --draft --title "<标题>" --body "<摘要与验证>" --base main --head <branch>
```

PR body 保持简洁，包含：

- 变更摘要
- 验证命令与结果
- 已知限制或未运行检查

## 决策规则

- 已有仓库优先复用：若 `git remote -v` 已有有效 `origin`，不要创建重复 GitHub 仓库。
- 仓库名优先来自用户；否则从目录名生成小写 kebab-case 名称。
- owner 优先来自用户；否则使用 `gh api user --jq .login` 获取当前登录用户。
- 默认分支优先使用现有分支；新仓库默认 `main`。
- 默认公开仓库，除非用户明确要求 private。
- 不提交密钥、token、`.env`、私有证书、大型构建产物或依赖目录。
- 不使用 `git reset --hard`、`git clean -fd`、`git checkout -- <file>` 等破坏性命令，除非用户明确要求。

## 常用命令片段

获取当前 GitHub 用户：

```bash
gh api user --jq .login
```

检查仓库是否存在：

```bash
gh repo view <owner>/<repo> --json nameWithOwner,visibility,url
```

创建公开仓库：

```bash
gh repo create <owner>/<repo> --public --source=. --remote=origin --push
```

打开仓库页面：

```bash
gh repo view --web
```

列出当前 PR 状态：

```bash
gh pr status
```

## 最终回复

完成后报告：

- GitHub 仓库 URL
- 推送的分支
- 提交哈希
- PR URL（如果创建）
- 已运行的验证命令
- 未完成或需要用户处理的事项，例如 `gh auth login`
