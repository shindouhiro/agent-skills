---
name: netlify-cli
description: Use when working with Netlify CLI (`netlify`) for site linking, `netlify.toml`, local Netlify Dev, builds, deploy previews, production deploys, environment variables, functions, monorepos, CI tokens, or troubleshooting Netlify deploy/runtime issues.
---

# Netlify CLI

> 以项目配置和可复现命令为中心使用 Netlify CLI；生产发布、远端环境变量和站点绑定都要显式确认目标站点与上下文。

## Preferences

- 优先读取项目现有的 `AGENTS.md`、`package.json`、锁文件、`netlify.toml`、`.nvmrc`、`.node-version`、`.netlify/state.json` 和 CI 配置。
- JavaScript/TypeScript 项目遵守项目包管理器；如果本地规范指定 `pnpm`，所有安装和脚本运行都使用 `pnpm`。
- 修改 Netlify 配置时优先使用 `netlify.toml`，但不要把密钥写入仓库；敏感变量通过 Netlify UI、CLI 或 CI secret 管理。
- 运行会影响远端状态的命令前，先确认站点、team、context 和是否生产发布；包括 `netlify deploy --prod`、`netlify env:set`、`netlify env:unset`、`netlify unlink`。
- CLI 行为可能随版本变化；遇到不确定选项时先运行 `netlify --version`、`netlify help <command>` 或查看官方命令参考。

## Workflow

1. 识别目标：本地开发、站点初始化/链接、构建验证、预览部署、生产部署、环境变量、函数或故障排查。
2. 检查项目上下文：确认根目录、monorepo 子包、build command、publish directory、functions directory、Node 版本和包管理器。
3. 检查 CLI 状态：确认 `netlify` 是否可用、是否已登录、项目是否已链接到正确 Site ID。
4. 按风险选择命令：本地命令可直接执行；远端变更先说明影响范围并使用明确 flags。
5. 验证结果：本地用 `netlify dev` / `netlify build` / 项目测试；部署用 deploy URL、CLI JSON 输出、Netlify 日志或站点状态确认。

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| CLI Workflows | 常用 Netlify CLI 任务、命令模式、`netlify.toml` 要点和排障清单。 | [cli-workflows](references/cli-workflows.md) |

## Quick Reference

### Command Pattern

```sh
netlify --version
netlify status
netlify link
netlify dev
netlify build
netlify deploy --build
netlify deploy --prod --build
netlify env:list --context production
```

### Minimal Output Pattern

```md
目标：<本地开发 / 链接站点 / 预览部署 / 生产部署 / 环境变量 / 排障>
项目：<根目录、框架、包管理器、build/publish/functions 配置>
命令：<执行或建议的 netlify 命令>
验证：<本地构建、deploy URL、日志、环境变量上下文>
结果：<完成状态、风险或后续动作>
```

## Validation Checklist

- 已确认当前目录是否为 Netlify 站点根目录，monorepo 场景已处理 base/filter。
- 已确认 `netlify.toml`、Netlify UI 设置和 CLI flags 是否存在冲突。
- 已确认 Node 版本、包管理器和本地构建命令与 Netlify 构建环境一致。
- 已避免提交 `.netlify/`、`.env`、令牌或密钥；必要时检查 `.gitignore`。
- 生产部署或环境变量写操作已明确目标 site、context、scope 和影响范围。
