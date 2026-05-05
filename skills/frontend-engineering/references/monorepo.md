# Monorepo 工程化基线

## 推荐目录

```text
.
├── apps/
│   ├── web/
│   ├── admin/
│   └── docs/
├── packages/
│   ├── ui/
│   ├── shared/
│   ├── config/
│   └── eslint-config/
├── pnpm-workspace.yaml
├── package.json
├── tsconfig.base.json
└── eslint.config.mjs
```

## pnpm workspace 与 Catalogs

`pnpm-workspace.yaml` 是 workspace 与 Catalogs 的唯一版本入口。默认 catalog 放通用依赖，命名 catalog 放框架族依赖。下面版本只演示写法；真实项目优先沿用现有版本，新增依赖前确认当前稳定版本再写入 Catalogs。

```yaml
packages:
  - 'apps/*'
  - 'packages/*'

catalog:
  '@antfu/eslint-config': ^5.0.0
  eslint: ^9.0.0
  typescript: ^5.0.0
  vite: ^7.0.0
  vitest: ^3.0.0
  tailwindcss: ^4.0.0

catalogs:
  react:
    react: ^19.0.0
    react-dom: ^19.0.0
    '@vitejs/plugin-react': ^5.0.0
  vue:
    vue: ^3.0.0
    '@vitejs/plugin-vue': ^6.0.0
    vue-tsc: ^3.0.0
  angular:
    '@angular/core': ^20.0.0
    '@angular/cli': ^20.0.0
    '@angular/compiler-cli': ^20.0.0
```

工作区 `package.json` 只引用 catalog，不直接写重复版本：

```json
{
  "dependencies": {
    "react": "catalog:react",
    "react-dom": "catalog:react",
    "@repo/shared": "workspace:*"
  },
  "devDependencies": {
    "typescript": "catalog:",
    "vite": "catalog:"
  }
}
```

安装规则：

```bash
pnpm add react react-dom --filter @repo/web
pnpm add -D @antfu/eslint-config eslint typescript -w
pnpm remove lodash --filter @repo/shared
pnpm exec tsc -b
pnpm dlx <cli-name>
```

安装后检查两件事：

- 新依赖版本是否进入 `pnpm-workspace.yaml`。
- 工作区 `package.json` 是否使用 `catalog:`、`catalog:<name>` 或 `workspace:*`。

## 根 package.json

根包保持私有，脚本负责聚合，具体任务放到各 workspace。

```json
{
  "private": true,
  "packageManager": "pnpm@<当前 pnpm 版本>",
  "scripts": {
    "dev": "pnpm -r --parallel dev",
    "build": "pnpm -r build",
    "lint": "pnpm -r lint",
    "typecheck": "pnpm -r typecheck",
    "test": "pnpm -r test"
  },
  "devDependencies": {
    "@antfu/eslint-config": "catalog:",
    "eslint": "catalog:",
    "typescript": "catalog:"
  }
}
```

`packageManager` 写入前先执行 `pnpm --version`，用当前项目实际版本替换占位。

## ESLint

根目录优先使用 flat config：

```js
import antfu from '@antfu/eslint-config'

export default antfu({
  formatters: true,
  stylistic: {
    indent: 2,
    quotes: 'single',
    semi: false,
  },
})
```

各工作区脚本：

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint:fix": "eslint . --fix"
  }
}
```

## Alias 与路径规则

所有跨目录、跨包、跨 feature 引入使用 alias。避免新增：

```ts
import { Button } from '../../../packages/ui/src/button'
```

优先改成：

```ts
import { Button } from '@repo/ui/button'
import { formatDate } from '@/shared/date'
```

基础 `tsconfig.base.json`：

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["apps/web/src/*"],
      "@repo/ui/*": ["packages/ui/src/*"],
      "@repo/shared/*": ["packages/shared/src/*"],
      "@repo/config/*": ["packages/config/src/*"]
    }
  }
}
```

包导出必须配合 `package.json` 的 `exports`，不要让消费者依赖内部深路径：

```json
{
  "name": "@repo/ui",
  "exports": {
    "./button": "./src/button/index.ts",
    "./input": "./src/input/index.ts"
  }
}
```

## 质量门禁

完成工程化改动后，优先运行：

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

如果仓库还没有这些脚本，补齐最小可用脚本，并在最终回复说明哪些命令已运行、哪些命令因为项目缺口无法运行。
