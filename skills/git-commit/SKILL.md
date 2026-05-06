---
name: git-commit
description: 智能分析代码变更并生成符合规范的中文 Git Commit 提交。
---

## 功能
此 Skill 旨在帮助用户快速完成 Git 提交，确保提交信息清晰、规范且符合中文习惯。

## 工作流程
1.  **检查状态**：
    -   运行 `git status` 查看变更文件。
    -   运行 `git diff` 或 `git diff --staged` 分析具体代码变更。

2.  **生成提交信息**：
    -   根据变更内容，生成符合 Conventional Commits 规范的提交信息。
    -   **格式**：`type: description`
    -   **语言**：简体中文
    -   **常用类型**：
        -   `feat`: 新功能 (feature)
        -   `fix`: 修补 bug
        -   `docs`: 文档 (documentation)
        -   `style`: 格式 (不影响代码运行的变动)
        -   `refactor`: 重构 (即不是新增功能，也不是修改 bug 的代码变动)
        -   `test`: 增加测试
        -   `chore`: 构建过程或辅助工具的变动

3.  **执行提交**：
    -   如果用户未提供具体消息，使用生成的消息。
    -   使用 `git commit -m "..."` 完成提交。

## 示例
-   `feat: 增加用户登录接口`
-   `fix: 修复首页样式错乱问题`
-   `chore: 更新依赖版本`

## 注意事项
-   如果存在 `AGENTS.md` 或其他项目规范文件，请优先遵循其中的 Commit 规范。
-   确保不要提交敏感信息（如密码、Key 等）。
