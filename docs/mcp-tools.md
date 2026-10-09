# RuleMetric MCP reference

Generated from actual MCP tool and resource discovery. Regenerate with `pnpm --filter @rulemetric/mcp exec tsx scripts/generate-tool-reference.ts`.

Private live records require RuleMetric authentication. Read-only annotations describe intended behavior; mutation tools remain separate. Runtime availability depends on the installed build, which may differ from this checkout.

## `rulemetric_admissions`

Read a page of persisted admissions, exclusions and preparation/experiment links for a canonical checkout. Follow nextOffset; total is server-side. Empty admissions do not explain every request rejected before intake.

- `projectPath` (required): string
- `offset`: integer; default 0

Structured output schema: yes. Read-only declaration: yes.

## `rulemetric_apply_suggestion`

Accept a precomputed instruction suggestion mid-session. The action depends on the suggestion's direction: an 'add' records acceptance and promotes a public instruction into your personal library; a 'remove' ARCHIVES the instruction (reversible soft delete); a 'trim' changes nothing and only acknowledges the advice. Records an accepted event either way so the suggestion stops being surfaced. IMPORTANT: archiving is a DATABASE action only — it stops RuleMetric proposing or injecting the instruction, and does NOT remove it from any file. Content that lives in CLAUDE.md, a skill file, a hook or an MCP server entry is still on disk and still loaded into every session after a remove is accepted. Use `rulemetric instructions retract` to remove it from disk.

- `suggestionId` (required): ID of the instruction_suggestions row
- `projectPath` (required): Absolute path to the project root

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_catalog`

Browse the org/team instruction catalog. Shows instructions visible to you based on your memberships.

- `badge`: Filter by badge; recommended, required
- `scope`: Filter by scope; org, team
- `search`: Search by name or description
- `limit`: Max results; default 20

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_diagnostics`

Run diagnostics on hook installation, config, API connectivity, and session state for a project path.

- `projectPath`: Absolute path to project root. Defaults to cwd.

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_evidence_get`

Read account-scoped evidence by kind and ID, independent of loaded history pages. This passive reader never settles or finalizes a trial. Follow nextOffset for observations. Canonical experiments use rulemetric_experiment_get.

- `kind` (required): string; study, live, trial, verdict, application, suggestion, promotion, eval-job, agent-job, eval-run, comparison, memory-experiment
- `id` (required): string
- `offset`: integer; default 0

Structured output schema: yes. Read-only declaration: yes.

## `rulemetric_experiment_get`

Read an existing account-owned experiment, its frozen protocol, findings, paginated observations, separate application receipts and saved operator decisions. Reading does not run or finalize it. Follow offset, applicationOffset and decisionOffset independently.

- `id` (required): string
- `offset`: integer; default 0
- `applicationOffset`: integer; default 0
- `decisionOffset`: integer; default 0

Structured output schema: yes. Read-only declaration: yes.

## `rulemetric_experiments_list`

Read a paginated canonical experiment inventory with server totals and separate execution, findings and application counts. Optional canonical checkout filter; follow nextOffset for the complete account-scoped inventory.

- `projectPath`: string
- `offset`: integer; default 0
- `limit`: integer; default 50

Structured output schema: yes. Read-only declaration: yes.

## `rulemetric_fork`

Fork an instruction to your personal scope, creating a linked copy that can track upstream changes.

- `instructionId` (required): ID of the instruction to fork

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_get`

Get full instruction detail (Layer 3: complete content + slots). Returns the entire instruction including content and slot values.

- `id` (required): Instruction ID

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_instruction_insights`

Get session-based performance insights and AI improvement suggestions for an instruction.

- `instructionId` (required): Instruction ID

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_list`

List available instructions (Layer 1: lightweight). Returns IDs, names, scope, and tags only — no content. Use rulemetric_summary or rulemetric_get to drill into details.

- `scope`: Filter by scope; personal, org, team, project
- `tags`: Comma-separated tags to filter by
- `limit`: Max results to return; default 20
- `offset`: Pagination offset; default 0

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_pipeline`

Read server-side pipeline counts for a canonical checkout and explicit UTC window (maximum 31 days). Includes population and sampling limitations; execution is separate from verified benefit and application.

- `projectPath` (required): string
- `since` (required): string
- `until` (required): string

Structured output schema: yes. Read-only declaration: yes.

## `rulemetric_promote`

Promote an instruction to a higher scope (personal → team → org).

- `instructionId` (required): ID of the instruction to promote
- `scope` (required): Target scope; team, org
- `scopeRef` (required): Target scope ref (team or org ID)

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_pull_upstream`

Pull upstream changes into a forked instruction using 3-way merge.

- `instructionId` (required): ID of the forked instruction

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_recommendations`

Call this when the user starts a new task, switches projects, or asks about codebase conventions you are not sure about. Returns ranked precomputed suggestions from the user's instruction library plus the public catalog.

Each suggestion has the shape { id, name, scope, score, reason, content }. The content field contains the full instruction body for high-score suggestions (score >= 0.7); lower-scoring suggestions return name and reason only. Cost: cheap — pulls from a precomputed cache, not an LLM call.

A score ranks a proposal; it does not establish measured benefit or authorize application. Read the reason and experiment evidence before choosing an action. rulemetric_apply_suggestion records a choice; repository file application has a separate path.

- `projectId` (required): ID of the project

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_search`

Search instructions by name, description, or content. Returns Layer 1 results (lightweight).

- `query` (required): Search query string
- `scope`: Filter by scope; personal, org, team, project
- `limit`: Max results; default 10

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_session_import`

Import a session from raw content (JSONL, JSON). Auto-detects tool format.

- `content` (required): Raw session content (JSONL for Claude Code, JSON for others)
- `tool`: Override auto-detection: claude_code, cursor, vscode_copilot

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_session_list`

List captured AI coding sessions. Filter by tool type.

- `tool`: Filter by AI tool; claude_code, cursor, vscode_copilot
- `limit`: Max results; default 10

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_summary`

Get instruction summary (Layer 2: metadata + slot names). Returns metadata, slot names, and a content preview — but not the full content. Use rulemetric_get for full content.

- `id` (required): Instruction ID

Structured output schema: no. Read-only declaration: not declared.

## `rulemetric_upstream`

Check if a forked instruction has upstream updates available.

- `instructionId` (required): ID of the forked instruction

Structured output schema: no. Read-only declaration: not declared.

## Resources

- `rulemetric://docs/suggestions.md`: Shared explanations for Test, Accept, Retire, Dismiss, budgets and evidence. Documentation is static; private live data requires authenticated tools.
- `instruction://{id}`: RuleMetric instruction content
