# Netlify CLI Workflows

## Official References

- CLI guide: https://docs.netlify.com/api-and-cli-guides/cli-guides/get-started-with-cli/
- Local development: https://docs.netlify.com/api-and-cli-guides/cli-guides/local-development/
- Deploy command reference: https://cli.netlify.com/commands/deploy/
- Env command reference: https://cli.netlify.com/commands/env/
- File-based configuration: https://docs.netlify.com/build/configure-builds/file-based-configuration/
- Functions with CLI: https://docs.netlify.com/cli/manage-functions/

## Project Discovery

Read these before changing behavior:

- `netlify.toml`: build command, publish directory, base directory, functions, redirects, headers, dev overrides.
- `.netlify/state.json`: local site link state. Do not edit by hand unless recovering a broken link.
- `package.json`: scripts, Netlify adapter/plugin packages, framework hints.
- Lock files: choose the existing package manager; in this workspace default to `pnpm`.
- CI files: confirm whether deploys are Git-based, CLI-based, or both.

Useful checks:

```sh
netlify --version
netlify status
netlify sites:list
netlify help
netlify help deploy
```

## Site Setup And Linking

Use `netlify login` for an interactive local session when the user wants browser-based auth.
Use `NETLIFY_AUTH_TOKEN` or `--auth` for CI and non-interactive automation.

Common flows:

```sh
netlify init
netlify link
netlify unlink
netlify open
```

Prefer `netlify link` for an existing manually cloned repository. Prefer `netlify init` when creating a new Netlify site or setting up continuous deployment.

After linking, verify:

- Site name / Site ID is expected.
- Team slug is expected when multiple teams exist.
- `.netlify/` is ignored by git.

## Local Development

Use Netlify Dev when the task depends on redirects, headers, functions, edge logic, or Netlify-managed environment variables:

```sh
netlify dev
netlify dev --context production
netlify dev:exec <command>
```

If framework detection is wrong, configure `[dev]` in `netlify.toml`:

```toml
[dev]
  command = "pnpm dev"
  targetPort = 3000
  port = 8888
  publish = "dist"
```

Remember `[dev].command` is not Bash; avoid shell-specific syntax there.

## Build And Deploy

Run a local Netlify build when validating Netlify-specific build plugins or environment behavior:

```sh
netlify build
```

Deploy patterns:

```sh
netlify deploy --build
netlify deploy --build --context deploy-preview
netlify deploy --prod --build
netlify deploy --dir dist --functions netlify/functions
netlify deploy --json
```

Use `--prod` only after the user confirms production deployment. Use `--json` when downstream automation needs the deploy URL or deploy ID. In monorepos, check current CLI support for `--filter` and confirm the package name or base directory.

If deploying functions manually, ensure dependencies are installed in the relevant package before `netlify deploy`, because the CLI bundles function dependencies from local `node_modules`.

## Environment Variables

Do not commit secrets. Prefer Netlify-managed environment variables for sensitive values.

Read variables:

```sh
netlify env:list
netlify env:list --context production
netlify env:get MY_VAR --scope functions
```

Write variables only after confirming context and scope:

```sh
netlify env:set MY_VAR value --context production --scope builds
netlify env:set MY_SECRET secret-value --context production --secret
netlify env:unset MY_VAR --context deploy-preview
netlify env:import .env
```

Context examples: `dev`, `production`, `deploy-preview`, `branch-deploy`, `branch:<branch-name>`.
Scope examples: `builds`, `functions`, `runtime`, `post-processing`.

Environment variable changes generally require a new build or deploy to affect deployed code.

## Functions

Common commands:

```sh
netlify functions:create
netlify functions:list
netlify functions:invoke <name>
netlify dev
```

Check `netlify.toml` for the functions directory:

```toml
[build]
  functions = "netlify/functions"
```

For local function testing, prefer `netlify dev` when the function depends on redirects, site context, or environment variables.

## Troubleshooting

- Build works locally but fails on Netlify: compare Node version, package manager, build command, publish directory, base directory, and environment variables.
- `netlify dev` serves the wrong app: inspect `[dev]` and framework detection, then set `command`, `targetPort`, `port`, and `publish`.
- Deploy points to the wrong site: run `netlify status`, inspect `.netlify/state.json`, then relink.
- Environment variable appears undefined: check context and scope, then trigger a new deploy.
- 404 after deploy: verify publish directory, SPA redirects, function path, and base directory.
- Function deploy/runtime failure: verify functions directory, dependency installation, bundler settings, and CLI debug logs.

Useful diagnostics:

```sh
netlify build --debug
netlify deploy --debug
netlify env:list --json
```
