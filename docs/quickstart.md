# RuleMetric quick start

RuleMetric measures whether changes to coding-agent instructions (CLAUDE.md, AGENTS.md, rules, memories, skills) improve results. Setup has four core steps: install the CLI, authenticate, install capture hooks, and run the background worker.

## 1. Install and authenticate

```bash
npm install -g @rulemetric/cli
rulemetric auth signup              # or: rulemetric auth login
rulemetric auth create-key --name "my-laptop"
```

`auth create-key` saves a long-lived API key (`rm_…`) to `~/.config/rulemetric/env` as `RULEMETRIC_API_KEY`; hooks, the proxy, the worker and the MCP server all read it from there. Accounts created with Google or GitHub have no password: create a key in the dashboard under Settings → Connections and run `rulemetric auth login --api-key <key>`.

## 2. Capture sessions

```bash
rulemetric hooks install            # current project
rulemetric hooks install --global   # every project for this user
```

What gets captured, by tool:

| Tool | How it is captured |
|---|---|
| Claude Code | Hooks. A project install writes `.claude/settings.local.json` (machine-local, gitignored); `--global` writes `~/.claude/settings.json`. The full transcript is reimported at session end. |
| Codex | The background worker imports transcripts from `~/.codex/sessions`. No hooks or proxy needed. |
| Cursor | Hooks in `.cursor/hooks.json` plus user-level `~/.cursor/hooks.json`. |
| GitHub Copilot (agent mode, Copilot CLI) | Hooks in `.github/hooks/rulemetric.json`. |
| Antigravity | Workspace hooks in `.agents/hooks.json`. |
| Pi | The background worker imports its session transcripts. |

Limit capture to specific directories with `rulemetric capture scope --add <dir>` (Codex sweep) or `rulemetric capture scope --tool hooks --add <dir>` (hook-driven tools). Without a scope, every project is captured.

The optional context proxy (`rulemetric proxy start`) additionally records full request context — system prompts, injected instructions, tool definitions — for supported model providers.

## 3. Run the background worker

```bash
rulemetric service install --worker-only
```

The worker prepares suggestions and runs experiments on your machine using agent tooling you already have installed (it auto-detects the `claude` CLI, then `codex`, then `copilot`, then `ANTHROPIC_API_KEY`). If none is found, jobs that need a model are skipped while still reporting success, so check `rulemetric diagnostics` if suggestions never appear.

## 4. Check it worked

```bash
rulemetric setup --json     # exit code 0 only when every core step is complete
rulemetric diagnostics      # capture, proxy and connectivity checks
rulemetric sessions list
```

Then open the dashboard: Pipeline (`/loop`) shows what the background loop measured and applied, Improvements (`/insights`) shows suggestions, and Experiments (`/evaluations`) shows comparison results.

## Local Mode (no cloud)

```bash
rulemetric local up          # requires Docker and the Supabase CLI; defaults to http://localhost:3000
rulemetric local down        # data preserved; add --delete-data to remove it
```

Local Mode runs the API, dashboard, and database on your machine. It requires Docker and the [Supabase CLI](https://supabase.com/docs/guides/cli).

No hosted RuleMetric account is required. Create an account on the local dashboard, or run `RULEMETRIC_API_URL=http://localhost:3000 rulemetric auth signup` to provision a local user. The optional `--email` and `--password` flags on `local up` can provision the first user when starting the runtime.

Local product services and storage do not make an external model provider local; agent requests use the model tooling and providers you configure.
