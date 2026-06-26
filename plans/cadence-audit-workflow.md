# Plan: ship `/cadence-audit` — the deep multi-agent companion to /context-health

> One change per plan. Adds a workflow-backed command to the kit.

## Goal
`/context-health` is rung-one (structural: markers, globs, missing notes — its own Notes say so). The
deep rungs — cross-reference integrity, convention consistency, model coherence, init-cadence /
context-health robustness, the install story, leanness — need fresh eyes a single pass can't give.
Ship that as a reusable, adversarially-verified **multi-agent review** any cadence repo can run:
`/cadence-audit`. It only reports; it edits nothing.

## In scope
- `.claude/workflows/cadence-audit.js` (new) — the 7-lens review → adversarial verify → synthesize.
- `.claude/commands/cadence-audit.md` (new) — the slash command that launches the workflow.
- `.claude/commands/context-health.md` — one line: point its "rung one" Note at `/cadence-audit` as the deep companion.
- `README.md` + `AGENTS.md` — list the new command.
- `.claude/VERSION` — bump.

## Out of scope
- Changing /context-health's structural behavior (the two are complementary).
- Making the audit edit anything (report-only, like context-health).

## Steps
1. Write the saved workflow: 7 lenses (xref, conventions, coherence, init, health, install, lean) as a
   pipeline → each finding refuted by an adversarial skeptic (default-refute, corrected_severity) →
   synthesize a prioritized, de-duplicated report + verdict (bulletproof | minor-gaps | real-gaps).
   Repo-agnostic (relative paths) so it runs in any repo that has the cadence.
2. Write `/cadence-audit` command — launches `Workflow({name:"cadence-audit"})`; flag it heavy (≈20–40 agents), report-only, opt-in.
3. Wire docs: context-health "rung one" pointer, README commands list, AGENTS map.
4. Bump VERSION; rebuild zip.

## Definition of done
- [ ] `/cadence-audit` launches the workflow; it returns a verdict + prioritized fixes, edits nothing.
- [ ] Workflow is self-contained (pure-literal meta) and repo-agnostic.
- [ ] Docs reference it; VERSION bumped; zip rebuilt.

## Constraints
- Opt-in power tool — heavy by design, never part of the default light loop. Keep the command lean.

## Progress
- Done: workflow + `/cadence-audit` command shipped, docs wired (README tree/buttons, AGENTS map,
  context-health "rung one" pointer), VERSION 0.4.5, zip rebuilt.
- Also folded in: the **8 findings from the first cadence-audit run** — the *major* README
  install/clobber gap (copy `.claude/` silently deletes an existing one); `(confirm)`-residue in
  context-health (old-model language); init-cadence no-git + monorepo branches; doctrine/grep-essay
  trims; guide monorepo note.
- Round 2 (re-audit, 0.4.5 → 0.4.6): fixed all 4 of its findings — 2 majors (same-day staleness
  false-negative + a note describing the opposite of git's behavior → pinned `00:00:00`, rewrote the
  note; `.claude/.claude` nesting undetected by any checkpoint → added detection to init-cadence
  pre-flight + context-health step 1), the 40/40 manual-reset vs auto-compaction collision (→ manual
  ~30%, override 40 as backstop, dropped "or lower"), and the step-6 density nit (→ sub-bulleted).
- Round 3 (0.4.6 → 0.4.7): caught that the round-2 threshold fix was **incomplete** — `plan.md` and
  `README.md` still said 40% (a regression I introduced). Fixed: all **5** reset surfaces now read
  30% manual / 40% backstop (grep-verified, no straggler). Also added the docs-routing scan signal to
  init-cadence step 1 (step 4's docs branch previously had no input). Findings trend: 8 → 4 → 2.
- Round 4 (0.4.7 → 0.4.8): verdict **minor-gaps — zero majors** (convergence). Fixed the 1 real minor
  (non-interactive defaults for the clobber + gitignore *blocking* gates) and 1 trivial nit (README:
  the copy can overwrite a target's own `.claude/.gitignore`). **Deliberately left** 1 borderline lean
  nit (guide/SKILL branch redundancy — the SKILL needs the rule inline to act and already points to
  the guide as the single definition; trimming risks a threshold-style regression for a stylistic gain).
- Round 5 (0.4.8 → 0.4.9, the user-requested confirming round): zero majors again. Fixed 3
  substantive minors — context-health enumerated via `git ls-files` (missed untracked/local-only
  notes → false 🟠); the `(confirm` grep scanned only `CLAUDE.md` (missed `AGENTS.md` + the round-4
  sidecar → false-green, a gap the round-4 fix itself created); the README never said how to *get* the
  kit. Left 2 cosmetic nits (dense guide paragraph, orphan optional review Pass 4).
- **Findings trend: 8(1maj) → 4(2maj) → 2(1maj) → 3(0maj) → 6(0maj).** Majors converged at round 4
  (zero in rounds 4 & 5). The minor/nit count does **not** reach zero — a thorough 7-lens adversarial
  audit always surfaces some polish, and each fix adds surface. **Loop stopped at 0.4.9:** bulletproof
  on substance; the remaining tail is cosmetic + inherent. `/cadence-audit` ships for on-demand polish.
