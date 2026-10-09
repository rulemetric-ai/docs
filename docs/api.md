# RuleMetric HTTP API reference

Base URL: `https://rulemetric.com` (hosted) or `http://localhost:3000` (Local Mode).

Authenticate every request with `Authorization: Bearer <token>`, where the token is an API key (`rm_…`, create one with `rulemetric auth create-key`) or a Supabase session JWT. Request and response bodies are JSON. Errors return a non-2xx status with a JSON body containing `error`.

This lists the endpoints most useful to integrations; it is not every route the dashboard uses.

## Instructions

| Method | Path | Description |
|---|---|---|
| GET | `/api/instructions` | List instructions (supports ?scope=, ?search=) |
| POST | `/api/instructions` | Create a new instruction |
| GET | `/api/instructions/:id` | Get instruction by ID |
| PUT | `/api/instructions/:id` | Update instruction (creates version on content change) |
| DELETE | `/api/instructions/:id` | Archive an instruction |

## Sessions

| Method | Path | Description |
|---|---|---|
| POST | `/api/sessions` | Create a new session |
| GET | `/api/sessions` | List sessions (?tool, ?outcome, ?friction, ?instructionId, ?projectPath) |
| GET | `/api/sessions/:id` | Get session with linked instructions |
| POST | `/api/sessions/:id/events` | Append event (atomic sequence assignment) |
| PATCH | `/api/sessions/:id/enrich` | Merge metadata, analysis, tokenUsage, externalSessionId |
| POST | `/api/sessions/:id/analyze` | AI-powered session analysis |
| POST | `/api/sessions/import` | Import from raw content (auto-detects tool) |
| POST | `/api/sessions/link-instructions` | Scan snapshots, auto-create + link instructions |

## Context Snapshots

| Method | Path | Description |
|---|---|---|
| POST | `/api/sessions/:id/context-snapshots` | Store captured LLM request snapshot |
| GET | `/api/sessions/:id/context-snapshots` | List snapshots (supports ?limit=, ?offset=) |
| GET | `/api/context-snapshots/:id` | Get single snapshot |

## Eval Targets & Cases

| Method | Path | Description |
|---|---|---|
| POST | `/api/eval-targets` | Create eval target |
| GET | `/api/eval-targets` | List targets (supports ?type=, ?content_hash=) |
| GET | `/api/eval-targets/:id` | Get target by ID |
| PUT | `/api/eval-targets/:id` | Update target (content change = new version) |
| POST | `/api/eval-targets/:id/evals` | Create test case for target |
| GET | `/api/eval-targets/:id/evals` | List test cases |

## Eval Runs & Benchmarks

| Method | Path | Description |
|---|---|---|
| POST | `/api/eval-runs` | Create eval run (posted by CLI worker) |
| GET | `/api/eval-targets/:id/runs` | List runs (supports ?iteration=, ?limit=) |
| POST | `/api/eval-targets/:id/benchmarks` | Create aggregated benchmark |
| GET | `/api/eval-targets/:id/benchmarks` | List benchmarks |

## Eval Jobs

| Method | Path | Description |
|---|---|---|
| POST | `/api/eval-jobs` | Create pending eval job (web UI triggers); the local CLI worker claims it via GET /api/work/next |
| GET | `/api/eval-targets/:id/jobs` | List jobs for a target |
| PATCH | `/api/eval-jobs/:id/progress` | Update job progress |
| PATCH | `/api/eval-jobs/:id/state` | Mirror state updates from worker (driven by runner.events) |
| POST | `/api/eval-jobs/:id/cancel` | Cancel a pending job |

## Eval from Context

| Method | Path | Description |
|---|---|---|
| POST | `/api/eval-from-context` | Find-or-create target + auto-generate evals + create job |

## Eval Conversations

| Method | Path | Description |
|---|---|---|
| POST | `/api/eval-targets/:id/conversations` | Get-or-create chat conversation |
| POST | `/api/eval-conversations/:id/messages` | Send user message (creates worker job) |
| POST | `/api/eval-conversations/:id/messages/assistant` | Worker posts assistant message |
| POST | `/api/eval-conversations/:id/apply-proposal` | Apply proposed content changes |

## Projects & Pipeline

| Method | Path | Description |
|---|---|---|
| GET | `/api/projects` | List projects with captured sessions |
| GET | `/api/projects/:id` | Get a project |
| GET | `/api/loop/summary` | Pipeline status: what the background loop measured, prepared and applied |

## Suggestions

| Method | Path | Description |
|---|---|---|
| GET | `/api/instruction-suggestions` | List instruction suggestions |
| GET | `/api/instruction-suggestions/:id` | Get a suggestion with its evidence |
| POST | `/api/instruction-suggestions/:id/events` | Record accept / dismiss events for a suggestion |

## Insights

| Method | Path | Description |
|---|---|---|
| GET | `/api/insights/compute` | Live aggregation (?projectPath, ?since, ?until) |
| GET | `/api/insights/instruction-impact` | With-vs-without comparison (?instructionId) |

## API Keys

| Method | Path | Description |
|---|---|---|
| GET | `/api/auth/api-keys` | List API keys for current user |
| POST | `/api/auth/api-keys` | Create new API key (returns key once) |
| DELETE | `/api/auth/api-keys/:id` | Revoke an API key |

## Worker Status

| Method | Path | Description |
|---|---|---|
| POST | `/api/workers/heartbeat` | Upsert worker connection heartbeat |
| GET | `/api/workers/status` | List all worker connections for user |
