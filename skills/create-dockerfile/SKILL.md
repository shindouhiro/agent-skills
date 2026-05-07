---
name: create-dockerfile
description: 为 Node.js/Next.js/Vite 应用创建生产级 Dockerfile 和 .dockerignore。Use when asked to containerize, dockerize, create Dockerfile, configure Docker, multi-stage build, or deploy to containers. Supports standalone Next.js, Vite SPA, and generic Node.js server applications.
metadata:
  author: Hikaru
  version: "2026.05.07"
  source: Docker best practices, Next.js deployment docs
---

# Create Dockerfile

> 为前端/Node.js 应用生成生产级多阶段构建 Dockerfile 和 .dockerignore。

## Preferences

- 优先使用项目已有的包管理器（pnpm > yarn > npm）。
- 多阶段构建：base → deps → build → runner，最小化最终镜像体积。
- 使用 `node:alpine` 系列基础镜像，除非项目有 native 依赖需要完整镜像。
- 非 root 用户运行应用（`node` 用户）。

## Workflow

1. 检查项目类型：Next.js（standalone）、Vite SPA、通用 Node.js 服务。
2. 检查包管理器：读取 `packageManager` 字段或 lockfile 类型。
3. 根据项目类型从 references 中选择对应模板。
4. 写入 `Dockerfile` 和 `.dockerignore` 到项目根目录。
5. 验证：`docker build -t <name> .`（如果 Docker 可用）。

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Next.js Standalone | Next.js standalone 模式的多阶段构建模板 | [nextjs.Dockerfile](references/nextjs.Dockerfile) |
| Vite SPA | Vite SPA + Nginx 的多阶段构建模板 | [vite-spa.Dockerfile](references/vite-spa.Dockerfile) |
| .dockerignore | 通用 .dockerignore 模板 | [dockerignore.template](references/dockerignore.template) |

## Quick Reference

### .dockerignore 必须排除的目录

```text
node_modules
.next
dist
.git
.env*
.DS_Store
```

### 构建测试

```bash
docker build -t <app-name> .
docker run -p 3000:3000 <app-name>
```

## Validation Checklist

- Dockerfile 使用多阶段构建，最终镜像不含 devDependencies 和构建工具。
- .dockerignore 覆盖 node_modules、构建产物、.git、环境文件。
- 使用非 root 用户运行。
- 包管理器与项目一致（pnpm/yarn/npm）。
- 端口暴露与应用实际监听端口匹配。
