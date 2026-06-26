# AGENTS.md

> Source of truth for any coding agent (Codex, Claude, Cursor, …) — tool-agnostic.
> Claude Code reads this via the `@AGENTS.md` line in `CLAUDE.md`.

## Project
This is the **master cadence** repo (`context-loop`): the canonical, drop-in system for how
agents and context work together. It produces the baseline docs/templates copied into new
repos, and is the yardstick for improving existing repos' docs. There is no application
code — it's docs, templates, a skill, and slash commands. It practices its own cadence:
when you change it, run the loop. The engine (`.claude/`) can be installed per-repo (default)
or globally (`~/.claude/`) for solo use; generated artifacts (`AGENTS.md`, `CLAUDE.md`,
`plans/`) always live in the repo.

## Commands
- Build / test / run: **none** — docs-and-templates repo. "Verification" = a cross-reference
  pass (every path/command referenced resolves) + confirming the `Describes:` / `Last verified:`
  marker format stays in sync between `.claude/templates/module-claude-md.md` and
  `.claude/commands/context-health.md`.

## Map
The whole drop-in cadence lives in **`.claude/`** (copy that one folder into any repo, or install the contents once into `~/.claude/` for solo global use — see *Delivery modes* in `.claude/guide/context.md`):
- `.claude/VERSION` — the cadence stamp (a `cadence: <x.y.z>` line); part of the copied payload, used to tell a target repo which version it has
- `.claude/skills/` — `context-loop` (the loop) + `init-cadence` (`/init-cadence`, bootstraps a repo)
- `.claude/commands/` — `/plan` `/review` `/context-health` `/cadence-audit`
- `.claude/templates/` — fill-in deliverables: `root-AGENTS.md`, `root-CLAUDE.md`,
  `module-claude-md.md`, `plan.md`
- `.claude/guide/context.md` — the context-file guide (what / why / when / how)
- `.claude/checklists/review.md` — the one-lens-at-a-time review passes
- `.claude/workflows/cadence-audit.js` — the multi-agent deep audit behind `/cadence-audit`

Root `AGENTS.md` + `CLAUDE.md` are this repo's own context (and what `/init-cadence` generates
for a target repo); `README.md` explains the repo. Too small for nested module files.

## Conventions
- Every non-trivial change goes through the **context-loop**: plan → small diffs →
  fresh-session review. The plan file is the source of truth across resets. (Claude-specific
  mechanics live in `CLAUDE.md` + the `context-loop` skill.)
- Refresh context in the **same diff** that changes behavior — updating docs is part of "done."

## Never do (without explicit approval)
- Change the marker format (`Describes:` / `Last verified:`) in one place only —
  `.claude/templates/module-claude-md.md` and `.claude/commands/context-health.md` must agree, or the
  audit breaks.
- Gut the seed files' intent (`.claude/templates/plan.md`, `.claude/checklists/review.md`) — refine, don't gut.
