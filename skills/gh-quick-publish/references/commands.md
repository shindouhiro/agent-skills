# GH Quick Publish 常用命令

## 工具与身份确认

```bash
gh --version
gh auth status
git --version
```

如果 `gh auth status` 失败，提示用户先执行 `gh auth login`。不要替用户处理、输出或保存 token。

## 读取项目状态

```bash
pwd
git status --short --branch
git remote -v
```

若目录不是 Git 仓库，使用 `git init -b main` 初始化。若已有用户改动，先说明将提交哪些文件；不要重置、覆盖或丢弃未明确属于本次任务的改动。

## 创建或关联 GitHub 仓库

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

## 提交代码

```bash
git status --short
git add <明确文件路径>
git status --short
git commit -m "<简洁中文或英文提交信息>"
```

只在确认提交范围后使用 `git add .`。提交信息描述用户可见结果，例如 `chore: initialize project`、`feat: add landing page`。

## 推送分支

```bash
git branch --show-current
git push -u origin <branch>
```

若是初始项目发布，通常直接推送 `main`。若是在现有仓库中交付新功能，创建 `codex/<short-task-name>` 分支后推送。

## 创建 PR

仅当用户要求 PR、当前工作在功能分支，或项目流程明显需要 review 时创建 PR。默认用 draft PR，除非用户明确要求 ready PR。

```bash
gh pr create --draft --title "<标题>" --body "<摘要与验证>" --base main --head <branch>
```

PR body 保持简洁，包含：

- 变更摘要
- 验证命令与结果
- 已知限制或未运行检查

## Docker CI Workflow（可选）

若用户同时需要 Docker 构建 CI：

1. 从 `references/docker-build.yml` 复制模板到 `.github/workflows/docker-build.yml`
2. 将模板中 `<repository-name>` 替换为实际项目名
3. 确保 GitHub 仓库已配置 `DOCKERHUB_USERNAME` 和 `DOCKERHUB_TOKEN` Secrets

## 常用查询

获取当前 GitHub 用户：

```bash
gh api user --jq .login
```

检查仓库是否存在：

```bash
gh repo view <owner>/<repo> --json nameWithOwner,visibility,url
```

打开仓库页面：

```bash
gh repo view --web
```

列出当前 PR 状态：

```bash
gh pr status
```
