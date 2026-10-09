# RuleMetric CLI reference

Generated from the command manifest of `@rulemetric/cli` 0.25.0. Install with `npm install -g @rulemetric/cli`.
Every command also accepts `--help`. Commands marked **JSON** accept `--json` for machine-readable output — prefer it when calling the CLI from an agent.

## antigravity

### `rulemetric antigravity backfill <conversationId>`

Backfill historical events from an Antigravity conversation transcript into a RuleMetric session. Useful for seeding a conversation that existed before hooks were installed — the live hook intentionally only captures forward.

Arguments:
- `conversationId` (required) — Antigravity conversationId (UUID) — the directory name under ~/.gemini/antigravity-ide/brain/

Flags:
- `--session <value>` (`-s`) — RuleMetric session UUID to POST events into. If omitted, resolved from the hook tmp file ($TMPDIR/rulemetric/<conversationId>) — fails if neither is present.
- `--transcript <value>` — Path to the transcript.jsonl file. Defaults to ~/.gemini/antigravity-ide/brain/<conversationId>/.system_generated/logs/transcript.jsonl
- `--after-step <value>` — Only emit records with step_index > this value. Default: read from existing state file, else -1 (everything).
- `--limit <value>` — Cap on the number of records to process this run.
- `--dry-run` — Classify records without POSTing. Reports counts only.
- `--save-state` — After backfill, persist the highest step_index so the live hook resumes from there.

```bash
rulemetric antigravity backfill 26fd215d-f43b-4ea5-b177-dc2c0e696f97 --session 158db9aa-68ee-43fc-961b-f3a180578694
rulemetric antigravity backfill 26fd215d-f43b-4ea5-b177-dc2c0e696f97 --dry-run
```

## auth

### `rulemetric auth create-key`

Create a long-lived API key for CLI and proxy authentication

Flags:
- `--name <value>` (`-n`) — Name for this key (required)
- `--save` (`-s`) — Save key to ~/.config/rulemetric/env for hooks/proxy

```bash
rulemetric auth create-key --name "laptop"
rulemetric auth create-key --name "ci token" --no-save
```

### `rulemetric auth login`

Log in to RuleMetric

Flags:
- `--email <value>` (`-e`) — Email address
- `--password <value>` (`-p`) — Password (for non-interactive use)
- `--api-key <value>` — Authenticate with an API key from https://rulemetric.com/connect (required for Google/GitHub sign-ins, which have no password)

```bash
rulemetric auth login
rulemetric auth login -e user@example.com
```

### `rulemetric auth logout`

Log out of RuleMetric

```bash
rulemetric auth logout
```

### `rulemetric auth revoke-key [id]`

Revoke an API key (run with no argument to list active keys)

Arguments:
- `id` — API key ID, prefix (rm_xxxxxxxx), or name

```bash
rulemetric auth revoke-key
rulemetric auth revoke-key rm_abc12345
rulemetric auth revoke-key "my-laptop (auto)"
```

### `rulemetric auth signup`

Create a RuleMetric account from the CLI

Flags:
- `--email <value>` (`-e`) — Email address
- `--password <value>` (`-p`) — Password (min 8 chars; for non-interactive use)

```bash
rulemetric auth signup
rulemetric auth signup -e user@example.com
```

### `rulemetric auth status`

Show current authentication status

```bash
rulemetric auth status
```

## capture

### `rulemetric capture scope`

Show or set independent project scopes for hooks and native transcript recovery

Flags:
- `--tool <value>` — Capture path: codex (default), pi, claude (historical recovery), opencode, or hooks. Claude recovery and OpenCode default off. (one of: `codex`, `pi`, `claude`, `opencode`, `hooks`; default: `codex`)
- `--add <value>` — Capture sessions under this path (repeatable). Implies an allowlist. (repeatable)
- `--remove <value>` — Stop capturing under this path (repeatable). (repeatable)
- `--off` — Turn this capture path off entirely.
- `--all` — Capture every project for this path.
- `--clear` — Remove this scope, restoring the default.

```bash
rulemetric <%= command.id %>
rulemetric <%= command.id %> --add ~/work/client-a --add ~/work/client-b
rulemetric <%= command.id %> --tool hooks --add ~/work/client-a
rulemetric <%= command.id %> --tool hooks --off
rulemetric <%= command.id %> --remove ~/work/client-a
rulemetric <%= command.id %> --off
rulemetric <%= command.id %> --all
```

## (top level)

### `rulemetric catalog`

Browse the org instruction catalog

Flags:
- `--badge <value>` — Filter by badge (one of: `recommended`, `required`)
- `--scope <value>` — Filter by scope (one of: `org`, `team`)
- `--search <value>` — Search by name or description

### `rulemetric dashboard`

Interactive TUI dashboard showing session analytics, outcomes, friction, and insights

Flags:
- `--period <value>` (`-p`) — Initial time period (one of: `today`, `week`, `30days`, `month`, `all`; default: `week`)

```bash
rulemetric dashboard
rulemetric dashboard --period 30days
```

### `rulemetric diagnostics`

Diagnose capture / proxy / connectivity problems (e.g. a client-side "502 Bad Gateway")

```bash
rulemetric diagnostics
```

### `rulemetric setup [target]` **JSON**

Agentic, transparent setup: plan / show diffs / apply the four core onboarding steps

Arguments:
- `target` — "plan" or a step id (install, auth, hooks, worker, judge)

Flags:
- `--json` — Machine-readable output
- `--show` — Print the step diff (read-only)
- `--apply` — Apply the step

```bash
rulemetric setup                 # interactive walk (TTY)
rulemetric setup plan            # read-only: every remaining artifact + diff
rulemetric setup hooks --show    # print one step's diff
rulemetric setup hooks --apply   # apply one step
rulemetric setup --json          # machine-readable status (exit 0 iff complete)
```

## catalog

### `rulemetric catalog recommend <projectId>`

Get AI-powered instruction recommendations for a project

Arguments:
- `projectId` (required) — ID of the project

## crons

### `rulemetric crons list`

List manually-runnable maintenance tasks

```bash
rulemetric crons list
```

### `rulemetric crons run <name>`

Run a maintenance task synchronously, right now

Arguments:
- `name` (required) — Task name (run `rulemetric crons list` to see all)

Flags:
- `--dry-run` — For Harbor, print the saved engine/model and supported tasks without running them
- `--quiet` (`-q`) — Suppress per-line log output; print summary only

```bash
rulemetric crons run cron_check_changelog
rulemetric crons run cron_suggest_instructions --quiet
```

## docs

### `rulemetric docs suggestions` **JSON**

Read the shared Suggestions action guide as Markdown (no authentication needed)

Flags:
- `--json` — Format output as json.

## eval-targets

### `rulemetric eval-targets create`

Create an eval target (instruction, skill, prompt, or plan to test)

Flags:
- `--name <value>` (`-n`) — Name for the eval target (required)
- `--type <value>` (`-t`) — Target type (required; one of: `instruction`, `skill`, `prompt`, `plan`)
- `--file <value>` (`-f`) — Path to content file
- `--content <value>` (`-c`) — Content string (alternative to --file)
- `--description <value>` (`-d`) — Description of the target (default: ``)

```bash
rulemetric eval-targets create --name my-rules --type instruction --file ./CLAUDE.md
rulemetric eval-targets create --name my-prompt --type prompt --content "Always use TypeScript"
```

### `rulemetric eval-targets list`

List eval targets

Flags:
- `--type <value>` (`-t`) — Filter by type (one of: `instruction`, `skill`, `prompt`, `plan`)

```bash
rulemetric eval-targets list
rulemetric eval-targets list --type instruction
```

## evals

### `rulemetric evals agent`

The worker daemon: long-polls /api/work/next, claims jobs, and runs them locally. Installed as a launchd service by `rulemetric service install`.

```bash
rulemetric evals agent
```

### `rulemetric evals auto-run <targetId>`

Retired adaptive loop; use evals run for one bounded experiment

Arguments:
- `targetId` (required) — Eval target ID

Flags:
- `--yes` (`-y`) — Actually run. Without this, only previews the plan + cost.
- `--max-runs <value>` — Hard ceiling on completed runs (default: `200`)
- `--batch-parallel <value>` — Concurrent executions within a batch (default: `3`)
- `--timeout <value>` — Timeout per eval in ms (default: `120000`)
- `--model <value>` — Model for eval execution
- `--grader-model <value>` — Model for grading
- `--skip-baseline` — Skip without_target runs (no delta — not recommended)

```bash
rulemetric evals run <target-id>
```

### `rulemetric evals benchmark <targetId>`

Aggregate eval runs into a benchmark for a target

Arguments:
- `targetId` (required) — Eval target ID

Flags:
- `--iteration <value>` — Filter runs by iteration (default: `1`)

```bash
rulemetric evals benchmark <target-id>
rulemetric evals benchmark <target-id> --iteration 2
```

### `rulemetric evals compare <targetA> <targetB>`

Retired blind comparison; use experiments compare for recorded task outcomes

Arguments:
- `targetA` (required) — First eval target ID
- `targetB` (required) — Second eval target ID

Flags:
- `--model <value>` — Model for execution and comparison
- `--timeout <value>` — Timeout per eval in ms (default: `120000`)

```bash
rulemetric experiments compare <experiment-id>
```

### `rulemetric evals create <targetId>`

Create test cases (evals) for an eval target

Arguments:
- `targetId` (required) — Eval target ID

Flags:
- `--name <value>` (`-n`) — Eval name
- `--prompt <value>` (`-p`) — Test prompt
- `--expectations <value>` (`-e`) — Assertion (repeatable) (repeatable)
- `--from-file <value>` — Bulk create from JSON file

```bash
rulemetric evals create <target-id> --name "ts-check" --prompt "Write a function to add two numbers"
rulemetric evals create <target-id> --from-file evals.json
```

### `rulemetric evals optimize <targetId>`

Retired optimization workflow; use canonical experiments

Arguments:
- `targetId` (required) — Eval target ID

Flags:
- `--auto` — Skip interactive review, apply suggestions automatically
- `--max-iterations <value>` — Maximum optimization iterations (default: `5`)
- `--threshold <value>` — Stop if pass rate reaches this threshold (0-1) (default: `0.95`)
- `--model <value>` — Model for eval execution
- `--analyzer-model <value>` — Model for analysis/suggestions
- `--timeout <value>` — Timeout per eval in ms (default: `120000`)

```bash
rulemetric evals run <target-id>
```

### `rulemetric evals optimize-description <targetId>`

Retired optimization workflow; use canonical experiments

Arguments:
- `targetId` (required) — Eval target ID

Flags:
- `--max-iterations <value>` — Maximum optimization iterations (default: `5`)
- `--model <value>` — Model for trigger testing and improvement
- `--queries <value>` — Number of trigger queries to generate (default: `20`)
- `--runs-per-query <value>` — Runs per query for reliability (default: `3`)
- `--poll-interval <value>` — Ms between approval polls (default: `5000`)
- `--timeout <value>` — Timeout per eval in ms (default: `120000`)

```bash
rulemetric evals run <target-id>
```

### `rulemetric evals refine-cases <targetId>`

Retire non-discriminating eval cases and regenerate discriminating replacements (§6e)

Arguments:
- `targetId` (required) — Eval target ID

Flags:
- `--yes` (`-y`) — Apply (retire + regenerate). Without this, only previews.
- `--no-generate` — Retire dead cases but do not regenerate replacements.

```bash
rulemetric evals refine-cases <target-id>            # preview
rulemetric evals refine-cases <target-id> --yes      # apply: retire + regenerate
rulemetric evals refine-cases <target-id> --yes --no-generate
```

### `rulemetric evals run <targetId>`

Queue an experiment against a target using the configured worker

Arguments:
- `targetId` (required) — Eval target ID

Flags:
- `--parallel <value>` — Number of parallel eval executions (default: `1`)
- `--runs-per-eval <value>` — Number of runs per eval for variance analysis (default: `1`)
- `--iteration <value>` — Iteration number for tracking (default: `1`)
- `--skip-baseline` — Skip baseline (without_target) runs
- `--skip-grading` — Skip LLM grading step
- `--model <value>` — Model for eval execution (default: user configured)
- `--grader-model <value>` — Model for grading (default: same as execution model)
- `--timeout <value>` — Timeout per eval in ms (default: `120000`)

```bash
rulemetric evals run <target-id>
rulemetric evals run <target-id> --parallel 3 --runs-per-eval 2
rulemetric evals run <target-id> --skip-baseline --skip-grading
```

### `rulemetric evals validate-judge <targetId>` **JSON**

Score a second model against your human judge-review labels (§6g validation pass)

Arguments:
- `targetId` (required) — Eval target ID

Flags:
- `--model <value>` — Second reviewer model. Default: resolved from your installed tooling (codex: your ~/.codex/config.toml model; claude: a tier that differs from the original judge).
- `--engine <value>` — CLI that runs the second reviewer. `auto` prefers your installed codex for a cross-vendor reviewer — two Claude models share a training lineage, so their agreement overstates independence. (one of: `auto`, `claude`, `codex`; default: `auto`)
- `--limit <value>` — Sample only the N most-recently-annotated runs (cost control / smoke)
- `--skip-reviewed` — Only re-grade assertions this reviewer model has not ruled on yet
- `--parallel <value>` — Concurrent re-grades (default: `3`)
- `--timeout <value>` — Timeout per re-grade in ms (default: `120000`)
- `--json` — Emit the full report as JSON

```bash
rulemetric evals validate-judge <target-id>                       # auto-detect installed CLIs
rulemetric evals validate-judge <target-id> --engine codex --model gpt-5.5
rulemetric evals validate-judge <target-id> --model claude-sonnet-5 --json
rulemetric evals validate-judge <target-id> --limit 5             # quick sample
```

## evidence

### `rulemetric evidence get <kind> <id>` **JSON**

Read persisted evidence without settling or finalizing its lifecycle

Arguments:
- `kind` (required) — Evidence kind
- `id` (required) — Evidence record ID

Flags:
- `--json` — Format output as json.
- `--offset <value>` — Observation offset (default: `0`)

## experiments

### `rulemetric experiments admissions` **JSON**

Show automatic captured-task admission, exclusions and experiment links

Flags:
- `--json` — Format output as json.
- `--project <value>` — Canonical project checkout path
- `--offset <value>` — Receipt pagination offset (default: `0`)

### `rulemetric experiments check <evidence>`

Check frozen engineering acceptance from a saved evidence export offline

Arguments:
- `evidence` (required) — Saved evidence JSON

### `rulemetric experiments compare <id>`

Run or inspect a frozen comparison; optionally compare two instruction files on verified source tasks

Arguments:
- `id` (required) — Frozen experiment UUID, or completed source UUID when comparing files

Flags:
- `--engine <value>` — Harness for both arms of the new comparison (one of: `claude`, `codex`)
- `--model <value>` — Explicit model pin for the selected harness
- `--before <value>` — UTF-8 baseline instruction file (empty content is allowed)
- `--after <value>` — UTF-8 treatment instruction file
- `--task <value>` — Source task ID; repeat to select tasks (default: all executed source tasks, maximum ten) (repeatable)
- `--reason <value>` — Why this new comparison is useful (at least 20 characters)
- `--request-id <value>` — Explicit UUID authorizing a distinct budget; identical arguments otherwise recover the original run
- `--wait <value>` — Seconds to wait for final results; 0 queues/inspects without waiting (default: `60`)
- `--output <value>` — Write evidence JSON to a new file; terminal exports include every receipt page

```bash
rulemetric experiments compare <experiment-id>
rulemetric experiments compare <completed-source-id> --before before.md --after after.md --reason "Check a shorter instruction on the same verified tasks" --wait 300
rulemetric experiments compare <experiment-id> --output evidence.json --wait 0
```

### `rulemetric experiments enroll`

Submit a fresh work request to an eligible project experiment

Flags:
- `--project <value>` (`-p`) — Project path (defaults to current directory)
- `--family <value>` — Task family shown by the waiting experiment (required)
- `--prompt-file <value>` — UTF-8 file containing a real, fresh work request (required)
- `--model <value>` — Optional model constraint; must match the experiment pin

```bash
rulemetric experiments enroll --project /repo --family parser-repair --prompt-file task.txt
```

### `rulemetric experiments get <id>` **JSON**

Read a frozen experiment, findings, observations and separate application receipts

Arguments:
- `id` (required) — Experiment ID

Flags:
- `--json` — Format output as json.
- `--offset <value>` — Observation offset (default: `0`)
- `--application-offset <value>` — Application receipt offset (default: `0`)
- `--decision-offset <value>` — Saved operator decision offset (default: `0`)

### `rulemetric experiments list` **JSON**

List experiments with execution, metric finding, and application separately

Flags:
- `--json` — Format output as json.
- `--project <value>` (`-p`) — Project path (defaults to current directory)
- `--all` — Every project
- `--limit <value>` — Page size (default: `50`)
- `--offset <value>` — Page offset; follow nextOffset (default: `0`)

```bash
rulemetric experiments list
rulemetric experiments list --all
```

### `rulemetric experiments reuse <id>`

Run selected previously measured fixture tasks with a new bounded exploratory budget

Arguments:
- `id` (required) — Completed source experiment UUID

Flags:
- `--task <value>` — Source task ID to reuse (repeat for multiple tasks) (required; repeatable)
- `--reason <value>` — Why this additional exploratory execution is useful (at least 20 characters) (required)
- `--request-id <value>` — Explicit UUID for a new budget; identical arguments otherwise recover the same request

### `rulemetric experiments try <task>`

Compare two instructions on one saved artifact task with Codex; review the checked results yourself

Arguments:
- `task` (required) — JSON task: prompt, saved files, checks, oracleFiles, incidentSessionIds and repositoryRevision

Flags:
- `--project <value>` — Source repository checkout (required)
- `--before <value>` — Baseline instruction file; empty content is allowed (required)
- `--after <value>` — Changed instruction file (required)
- `--model <value>` — Exact Codex model pin for both arms (MODEL or codex:MODEL) (required)
- `--reason <value>` — Why this comparison is useful (default: `Operator-selected single-task instruction comparison.`)
- `--timeout <value>` — Maximum seconds per actor run; two runs, each may make multiple model requests and tool turns (default: `120`)
- `--request-id <value>` — Explicit UUID authorizing a distinct pair; identical arguments otherwise recover the existing run
- `--wait <value>` — Seconds to wait; 0 queues and returns the experiment ID (default: `60`)
- `--output <value>` — Export evidence to a new JSON file

```bash
rulemetric experiments try task.json --project /path/to/repo --before before.md --after after.md --model gpt-6.1-sol --wait 300
```

## hooks

### `rulemetric hooks install`

Install session tracking hooks for coding tools and the context capture proxy

Flags:
- `--no-proxy` — Skip proxy/gateway configuration (hooks only)
- `--project-dir <value>` — Project directory to install hooks in (defaults to cwd)
- `--user-hooks` — Write user-level Codex/Cursor/Antigravity hooks, plus Copilot CLI hooks with --global. Required for Cursor capture — its traffic bypasses the proxy.
- `--global` (`-g`) — Install user-level hooks; capture scope controls which projects are tracked
- `--statusline-usage` — Install the statusline shim so Claude Code pushes live rate_limits on every refresh (makes /usage rings update in real time without needing the proxy)

```bash
rulemetric hooks install
rulemetric hooks install --global
rulemetric hooks install --no-proxy
```

### `rulemetric hooks uninstall`

Remove session tracking hooks from a project (or the global install)

Flags:
- `--global` — Remove the GLOBAL install (~/.claude) rather than this project
- `--project-dir <value>` — Project directory to clean (defaults to cwd)
- `--user-hooks` — Also strip user-level Codex/Cursor/Antigravity/Copilot hooks

```bash
rulemetric hooks uninstall
rulemetric hooks uninstall --global
rulemetric hooks uninstall --project-dir ~/work/repo-a
rulemetric hooks uninstall --no-user-hooks
```

## instructions

### `rulemetric instructions create [file]`

Create a new instruction

Arguments:
- `file` — Path to markdown file

Flags:
- `--name <value>` (`-n`) — Instruction name (required)
- `--description <value>` (`-d`) — Instruction description (required)
- `--scope <value>` (`-s`) — Instruction scope (one of: `org`, `team`, `project`, `personal`; default: `personal`)
- `--scope-ref <value>` — Scope reference UUID
- `--tags <value>` (`-t`) — Comma-separated tags

```bash
rulemetric instructions create ./my-instruction.md --name "My Rules" --description "Team coding rules"
rulemetric instructions create ./rules.md -n "Rules" -d "Project rules" -s project
```

### `rulemetric instructions dedupe`

Report instructions in this project that repeat each other in different words

Flags:
- `--dir <value>` — Directory whose CLAUDE.md to check (ancestors are included) (default: `.`)
- `--threshold <value>` — Similarity floor 0-1 for calling two asks the same (default 0.3)

```bash
rulemetric instructions dedupe
rulemetric instructions dedupe --dir apps/cli
```

### `rulemetric instructions delete <id>`

Delete (archive) an instruction

Arguments:
- `id` (required) — Instruction ID

Flags:
- `--force` (`-f`) — Skip confirmation prompt

```bash
rulemetric instructions delete <id>
rulemetric instructions delete <id> --force
```

### `rulemetric instructions fork <id>`

Fork an instruction to your personal scope

Arguments:
- `id` (required) — ID of the instruction to fork

```bash
rulemetric instructions fork <instruction-id>
```

### `rulemetric instructions get <id>` **JSON**

Get an instruction by ID

Arguments:
- `id` (required) — Instruction ID

Flags:
- `--json` — Format output as json.
- `--format <value>` (`-f`) — Output format (one of: `yaml`, `json`, `raw`; default: `yaml`)

```bash
rulemetric instructions get <id>
rulemetric instructions get <id> --format raw
```

### `rulemetric instructions list` **JSON**

List your instructions

Flags:
- `--json` — Format output as json.
- `--format <value>` (`-f`) — Output format (one of: `table`, `json`; default: `table`)

```bash
rulemetric instructions list
rulemetric instructions list --json
```

### `rulemetric instructions promote <id>`

Promote an instruction to a higher scope (personal → team → org)

Arguments:
- `id` (required) — ID of the instruction to promote

Flags:
- `--scope <value>` — Target scope (required; one of: `team`, `org`)
- `--ref <value>` — Target scope ref (team or org ID) (required)

```bash
rulemetric instructions promote <id> --scope team --ref <team-id>
rulemetric instructions promote <id> --scope org --ref <org-id>
```

### `rulemetric instructions pull <id>`

Pull upstream changes into a forked instruction

Arguments:
- `id` (required) — ID of the forked instruction

### `rulemetric instructions restore <id>`

Restore an archived instruction

Arguments:
- `id` (required) — Instruction ID

```bash
rulemetric instructions restore <id>
```

### `rulemetric instructions retract [id]`

Remove an instruction, skill, hook or MCP server from disk (dry-run by default)

Arguments:
- `id` — Instruction ID (omit when using --kind/--target)

Flags:
- `--apply` — Actually write. Without this, nothing is modified.
- `--force` — Proceed without a randomised verdict (observational evidence only)
- `--kind <value>` — Retract something that is not an instruction row (one of: `claude-md`, `memory`, `skill`, `hook`, `mcp`)
- `--target <value>` — File path, skill name, MCP server name, or hook command substring
- `--heading <value>` — Markdown heading to remove (defaults to the instruction name)

```bash
rulemetric instructions retract <instruction-id>
rulemetric instructions retract <instruction-id> --apply
rulemetric instructions retract --kind skill --target agent-browser
rulemetric instructions retract --kind mcp --target supabase --apply
rulemetric instructions retract --kind hook --target "doomed-tool" --apply
```

### `rulemetric instructions upstream <id>`

Check if a forked instruction has upstream updates

Arguments:
- `id` (required) — ID of the forked instruction

### `rulemetric instructions versions <id>` **JSON**

List version history for an instruction

Arguments:
- `id` (required) — Instruction ID

Flags:
- `--json` — Format output as json.

```bash
rulemetric instructions versions <id>
```

## local

### `rulemetric local down`

Stop the local RuleMetric server and database stack. Data is preserved (restart with `rulemetric local up`); use --delete-data to also wipe the local database.

Flags:
- `--delete-data` — Also delete the local database volumes (irreversible)

### `rulemetric local up`

Run RuleMetric fully locally: start a local Supabase stack (Docker), apply migrations, and serve the API + dashboard from the @rulemetric/local runtime — all data on this machine, zero cloud contact. Downloads the runtime package on first run.

Flags:
- `--port <value>` (`-p`) — Local API + dashboard port (default: `3000`)
- `--email <value>` (`-e`) — Create the first user with this email (with --password)
- `--password <value>` — Password for the first user (min 8 chars)
- `--foreground` — Run the server in this terminal instead of installing a launchd service (works on any OS; skips user provisioning)
- `--yes` (`-y`) — Skip the runtime-download confirmation

```bash
rulemetric local up --email you@example.com --password <pw>
rulemetric local up --port 3000
rulemetric local up --foreground
```

## loop

### `rulemetric loop pipeline` **JSON**

Read pipeline counts and limitations for a project and explicit time window

Flags:
- `--json` — Format output as json.
- `--project <value>` — Canonical checkout path (defaults to current directory)
- `--since <value>` — Window start, ISO UTC timestamp (required)
- `--until <value>` — Window end, ISO UTC timestamp; at most 31 days after start (required)

## mcp

### `rulemetric mcp serve`

Serve RuleMetric MCP tools over stdio using the saved API credentials

## org

### `rulemetric org current`

Show the current active organization

```bash
rulemetric org current
```

### `rulemetric org list`

List organizations the current user is a member of

```bash
rulemetric org list
```

### `rulemetric org switch [slug]`

Switch the active organization

Arguments:
- `slug` — Organization slug to switch to

Flags:
- `--clear` — Clear the active organization (personal mode)

```bash
rulemetric org switch acme
rulemetric org switch --clear
```

## packs

### `rulemetric packs add-instruction <packId> <instructionId>`

Add an instruction to a pack

Arguments:
- `packId` (required) — Pack ID
- `instructionId` (required) — Instruction ID

```bash
rulemetric packs add-instruction <pack-id> <instruction-id>
```

### `rulemetric packs apply <id>`

Apply a pack to a project (bulk-link all instructions)

Arguments:
- `id` (required) — Pack ID

Flags:
- `--project <value>` (`-p`) — Project ID (required)

```bash
rulemetric packs apply <pack-id> --project <project-id>
```

### `rulemetric packs create`

Create a new instruction pack

Flags:
- `--name <value>` (`-n`) — Pack name (required)
- `--description <value>` (`-d`) — Pack description (required)
- `--scope <value>` (`-s`) — Pack scope (one of: `org`, `team`, `personal`; default: `personal`)
- `--tags <value>` (`-t`) — Comma-separated tags

```bash
rulemetric packs create --name "Security Pack" --description "Security-focused instructions"
```

### `rulemetric packs fork <id>`

Fork a pack to your personal scope

Arguments:
- `id` (required) — ID of the pack to fork

```bash
rulemetric packs fork <pack-id>
```

### `rulemetric packs get <id>`

Get pack detail with instruction list

Arguments:
- `id` (required) — Pack ID

```bash
rulemetric packs get <pack-id>
```

### `rulemetric packs list` **JSON**

List your instruction packs

Flags:
- `--json` — Format output as json.
- `--format <value>` (`-f`) — Output format (one of: `table`, `json`; default: `table`)

```bash
rulemetric packs list
rulemetric packs list --json
```

### `rulemetric packs remove-instruction <packId> <instructionId>`

Remove an instruction from a pack

Arguments:
- `packId` (required) — Pack ID
- `instructionId` (required) — Instruction ID

```bash
rulemetric packs remove-instruction <pack-id> <instruction-id>
```

## proxy

### `rulemetric proxy env`

Print shell env vars to route any client through the RuleMetric proxy

Flags:
- `--shell <value>` (`-s`) — Shell format (posix, fish, powershell) (one of: `posix`, `fish`, `powershell`; default: `posix`)
- `--python` — Include Python-specific env vars (REQUESTS_CA_BUNDLE, SSL_CERT_FILE)
- `--node` — Include Node.js-specific env vars (NODE_EXTRA_CA_CERTS)
- `--all` — Include env vars for all runtimes
- `--vscode` — Output VS Code settings.json snippet for Copilot proxy

```bash
rulemetric proxy env
rulemetric proxy env --shell fish
eval "$(rulemetric proxy env)"
rulemetric proxy env --python
```

### `rulemetric proxy logs`

View context capture proxy logs

Flags:
- `--follow` (`-f`) — Follow log output (like tail -f)
- `--lines <value>` (`-n`) — Number of lines to show (default: `30`)

```bash
rulemetric proxy logs
rulemetric proxy logs --follow
rulemetric proxy logs --lines 50
```

### `rulemetric proxy restart`

Restart the context capture proxy (stop + start)

Flags:
- `--port <value>` (`-p`) — Proxy listen port
- `--docker` (`-d`) — Run in Docker mode

```bash
rulemetric proxy restart
```

### `rulemetric proxy setup`

Set up context capture proxy (installs mitmproxy, configures shell)

Flags:
- `--shell <value>` — Shell to configure (auto-detected if omitted) (one of: `zsh`, `bash`, `fish`, `powershell`)
- `--port <value>` (`-p`) — Proxy listen port (default: `8787`)
- `--check` — Only check prerequisites, do not install anything

```bash
rulemetric proxy setup
rulemetric proxy setup --shell zsh
rulemetric proxy setup --check
```

### `rulemetric proxy start`

Start context capture proxy

Flags:
- `--port <value>` (`-p`) — Proxy listen port (default: `8788`)
- `--docker` (`-d`) — Run proxy in Docker (no local mitmproxy needed)
- `--web` — Launch mitmweb debug UI instead of mitmdump (local mode only)
- `--skip-checks` — Skip prerequisite checks
- `--build` — Force rebuild Docker image

```bash
rulemetric proxy start
rulemetric proxy start --docker
rulemetric proxy start --port 9090
rulemetric proxy start --web
```

### `rulemetric proxy status`

Check gateway and context capture proxy status

```bash
rulemetric proxy status
```

### `rulemetric proxy stop`

Stop the context capture proxy

```bash
rulemetric proxy stop
```

## research

### `rulemetric research backfill`

Scan every ~/.claude/projects transcript for past rate-limit (429) events and POST them as observations. Opt-in seed for the usage-research dataset — forward-only tail is the default capture path.

Flags:
- `--claude-dir <value>` — Override the Claude Code projects directory (defaults to ~/.claude)
- `--dry-run` — Print observation count without POSTing

```bash
rulemetric research backfill
rulemetric research backfill --claude-dir /custom/.claude --dry-run
```

## service

### `rulemetric service install`

Install RuleMetric services. Default (in a repo checkout): API + worker + gateway + capture proxy as launchd agents. With --worker-only: just the job worker, targeting the globally-installed CLI — no repo, no build (works from `npm i -g @rulemetric/cli`, macOS launchd or Linux systemd).

Flags:
- `--port <value>` (`-p`) — API server port (default: `3000`)
- `--force` (`-f`) — Overwrite existing service configuration and reclaim conflicting ports
- `--skip-build` — Skip the pre-install workspace build (API/web/cli dist must already exist)
- `--no-proxy` — Skip gateway + mitmproxy plists (just install API + worker)
- `--auto-update` — Opt this machine into automatic stable CLI updates after active worker jobs finish (requires --worker-only)
- `--worker-only` — Install ONLY the job worker as a persistent service, targeting the globally-installed CLI (no repo checkout, no build, no API/gateway/proxy). Sources auth from ~/.config/rulemetric/env. macOS (launchd) + Linux (systemd --user).

```bash
rulemetric service install
rulemetric service install --port 3000
rulemetric service install --skip-build
rulemetric service install --no-proxy
rulemetric service install --worker-only
```

### `rulemetric service status`

Check the status of installed RuleMetric services (launchd on macOS; systemd --user worker on Linux)

### `rulemetric service uninstall`

Remove RuleMetric services (macOS launchd: API/worker/gateway/proxy; Linux: the --worker-only systemd unit)

### `rulemetric service updates`

Show or change automatic worker updates on this machine (off by default)

Flags:
- `--enable` — Install stable npm releases automatically after active jobs finish
- `--disable` — Stop future automatic updates

```bash
rulemetric service updates
rulemetric service updates --enable
rulemetric service updates --disable
```

## sessions

### `rulemetric sessions analyze <id>`

Trigger AI analysis on a session

Arguments:
- `id` (required) — Session ID

```bash
rulemetric sessions analyze <session-id>
```

### `rulemetric sessions backfill-reasoning`

Audit retained transcripts for reasoning usage; optionally import them with the current parser

Flags:
- `--directory <value>` — Explicit transcript directory to scan recursively (only select projects you want imported) (required)
- `--tool <value>` (required; one of: `claude_code`, `codex`)
- `--since <value>` — Earliest session end date, YYYY-MM-DD (UTC) (required)
- `--apply` — Upload matching transcripts, updating existing sessions or importing uncaptured ones

### `rulemetric sessions delete [ids]`

Permanently delete captured sessions and everything stored with them (events, context snapshots, offloaded blobs)

Arguments:
- `ids` — Session id(s) to delete

Flags:
- `--project <value>` — Delete every session captured under this project path (use with --yes).
- `--yes` — Skip the confirmation prompt. Required for --project in non-interactive use.

```bash
rulemetric sessions delete 4f9d…
rulemetric sessions delete 4f9d… 8a21…
rulemetric sessions delete --project /work/client-a --yes
```

### `rulemetric sessions end`

End the active capture session

```bash
rulemetric sessions end
```

### `rulemetric sessions import <path>`

Import a session file (JSONL, JSON)

Arguments:
- `path` (required) — Path to session file

Flags:
- `--tool <value>` — Override tool detection (claude_code, cursor, vscode_copilot)

```bash
rulemetric sessions import ~/.claude/projects/my-project/session-abc.jsonl
rulemetric sessions import ./copilot-export.json --tool vscode_copilot
```

### `rulemetric sessions list`

List captured sessions

Flags:
- `--tool <value>` — Filter by tool (claude_code, cursor, vscode_copilot, copilot_agent, rulemetric_mcp, codex, pi, opencode, antigravity)
- `--limit <value>` — Max results (default: `20`)

```bash
rulemetric sessions list
rulemetric sessions list --tool claude_code
rulemetric sessions list --tool codex
```

### `rulemetric sessions recover-projects`

Review home-launched sessions for Git repository evidence; optionally backfill and reanalyze

Flags:
- `--limit <value>` — Maximum home-launched sessions to review (default: `200`)
- `--apply` — Save verified project assignments and recovery evidence
- `--reanalyze` — Reanalyze reviewed sessions locally, even if they already have analysis

### `rulemetric sessions start`

Start a capture session for any LLM provider (Cortex, Groq, etc.)

Flags:
- `--tool <value>` (`-t`) — LLM provider or tool name (e.g., cortex, groq, deepseek, custom_app) (required)
- `--project-dir <value>` (`-d`) — Project directory (defaults to cwd)

```bash
rulemetric sessions start --tool cortex
rulemetric sessions start --tool groq --project-dir /path/to/project
eval "$(rulemetric proxy env --python)" && rulemetric sessions start --tool cortex
```

## skills

### `rulemetric skills export-approved`

Export approved community submissions as community-skills.json for registry merge

Flags:
- `--output <value>` — Output file path (default: `packages/skills-registry/community-skills.json`)

```bash
rulemetric skills export-approved
rulemetric skills export-approved --output ./community-skills.json
```

### `rulemetric skills install <id>`

Install a skill from the registry into your project

Arguments:
- `id` (required) — Skill ID to install

Flags:
- `--preview` — Preview content without writing
- `--dir <value>` — Project directory (default: `.`)
- `--force` — Replace an existing skill directory at the Claude Code path

```bash
rulemetric skills install tdd-workflow
rulemetric skills install tdd-workflow --preview
rulemetric skills install tdd-workflow --force
```

### `rulemetric skills list`

List skills in the registry

Flags:
- `--stats` — Show registry statistics
- `--limit <value>` — Max skills to show (default: `30`)

```bash
rulemetric skills list
rulemetric skills list --stats
```

### `rulemetric skills publish <file>`

Submit a skill file to the RuleMetric skills registry

Arguments:
- `file` (required) — Path to skill file (.md)

Flags:
- `--name <value>` — Skill name (defaults to filename)
- `--description <value>` — One-line description
- `--category <value>` — Skill category (one of: `workflow`, `language`, `framework`, `tool`, `security`, `testing`, `architecture`, `devops`, `documentation`, `performance`, `other`; default: `other`)
- `--tool <value>` — Primary tool this skill targets (one of: `claude_code`, `cursor`, `copilot`, `windsurf`, `universal`; default: `universal`)
- `--tags <value>` — Comma-separated tags (default: ``)

```bash
rulemetric skills publish ./my-skill.md
rulemetric skills publish ./my-skill.md --category workflow --tool claude_code
```

### `rulemetric skills search [query]`

Search the community skills registry

Arguments:
- `query` — Search term

Flags:
- `--tool <value>` — Filter by tool (one of: `claude_code`, `cursor`, `copilot`, `windsurf`, `cline`, `universal`)
- `--category <value>` — Filter by category (one of: `workflow`, `language`, `framework`, `tool`, `security`, `testing`, `architecture`, `devops`, `documentation`, `performance`)
- `--language <value>` — Filter by programming language
- `--framework <value>` — Filter by framework
- `--limit <value>` — Max results (default: `20`)

```bash
rulemetric skills search "tdd"
rulemetric skills search --tool cursor --category testing
rulemetric skills search --language python
```

### `rulemetric skills update`

Fetch latest skills from GitHub sources and update local registry

Flags:
- `--all` — Include Tier 2 (niche) sources

```bash
rulemetric skills update
rulemetric skills update --all
```

## suggestions

### `rulemetric suggestions accept <id>`

Accept a suggestion and append the instruction to CLAUDE.md

Arguments:
- `id` (required) — Suggestion UUID from `suggestions list`

Flags:
- `--force` — Add the rule even if this project already says the same thing in other words

```bash
rulemetric suggestions accept <id>
```

### `rulemetric suggestions auto`

Show or change automatic adoption of high-confidence suggestions into CLAUDE.md

Flags:
- `--on` — Enable automatic adoption
- `--off` — Disable automatic adoption
- `--project <value>` — Apply to one project only (overrides the account default)
- `--max-per-window <value>` — How many rules the loop may adopt per project per window (0 stops it adopting)
- `--max-per-run <value>` — How many rules one nightly run may adopt
- `--window-days <value>` — Length of the budget window, in days
- `--min-score <value>` — Minimum suggestion score the loop may adopt (0–1)
- `--reset` — Clear the budget overrides at this level and fall back to the shipped defaults

```bash
rulemetric suggestions auto
rulemetric suggestions auto --off
rulemetric suggestions auto --on
rulemetric suggestions auto --off --project <project-id>
```

### `rulemetric suggestions dismiss <id>`

Dismiss a suggestion so it will not re-surface for 30 days

Arguments:
- `id` (required) — Suggestion UUID from `suggestions list`

```bash
rulemetric suggestions dismiss <id>
```

### `rulemetric suggestions list`

List instruction suggestions for a project

Flags:
- `--project <value>` (`-p`) — Project path (defaults to current directory)

```bash
rulemetric suggestions list
rulemetric suggestions list --project /path/to/project
```

### `rulemetric suggestions refresh`

Information about refreshing instruction suggestions

```bash
rulemetric suggestions refresh
```

### `rulemetric suggestions undo <instructionId>`

Remove an adopted instruction from this project's agent instruction files and stop it being re-proposed

Arguments:
- `instructionId` (required) — Instruction ID from the rulemetric:start marker in the instruction file

```bash
rulemetric suggestions undo <instruction-id>
```
