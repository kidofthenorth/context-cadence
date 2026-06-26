# Handoff: execute the cadence-audit fixes on `context-cadence`

> Work order for an executing Opus session. Produced 2026-06-26 from a `/cadence-audit` run
> (verdict `real-gaps`, 9 findings, all adversarially verified). Report-only audit is done — this
> session **implements** the fixes and reports to a reviewer chat. The companion plan the executor
> writes is `plans/cadence-audit-fixes.md`.

You are Opus working in the **`context-cadence`** repo (`e:\repos\context-cadence`) — the *master
cadence engine*. It has **no application code**: it's docs, templates, a skill, and slash commands
that ship as a drop-in `.claude/` folder plus root `AGENTS.md`/`CLAUDE.md`. It dogfoods its own
"context-loop" cadence, so you must too.

A deep multi-agent audit (`/cadence-audit`) returned verdict **`real-gaps`**: 9 findings, all of
which survived adversarial refutation (re-verified against live git behavior; several reproduced
empirically). Your job is to **implement the fixes**, then **post a report to the reviewer chat**.
The audit was report-only — you are the one who edits.

## Method — use the repo's own cadence (non-negotiable)
1. **Branch first** — do not commit to `main` directly. Follow the repo's commit conventions.
2. **Plan before editing.** Write a short plan to `plans/cadence-audit-fixes.md` (use
   `.claude/templates/plan.md` as the shape) listing the fixes, the files each touches, and the
   order. The plan file is the source of truth across context resets.
3. **Small diffs.** One finding (or one tightly-coupled cluster) per diff. After each, re-read the
   file you changed.
4. **Refresh context in the same diff that changes behavior** — updating docs *is* "done" here;
   there's no separate code to verify.
5. **Re-verify line numbers against the live files** — the line refs below are a snapshot and will
   shift as you edit. Read before you edit.

## Hard constraints (the repo's "Never do" — violating these breaks the kit)
- **Marker never-do:** the `Describes:` / `Last verified:` marker format must stay
  **byte-identical** between `.claude/templates/module-claude-md.md` and
  `.claude/commands/context-health.md`. If a fix touches the marker in one, make the *same* change
  in the other. (The audit confirmed they're currently byte-identical via hexdump — keep them that
  way.)
- **Enumerated lists that appear in >1 file must stay in sync** — e.g. the context-health finding
  categories. If you touch them in one place, reconcile the others.
- **Don't gut seed files' intent** (`plan.md`, `review.md`) — refine, don't rewrite.
- **Leanness ethos:** "add a sentence, not a framework; refine, don't gut; keep it short — it's
  always in the window." Prefer the smallest edit that closes the gap. Several findings are
  *over-statement* problems; don't fix bloat by adding more bloat.
- **Report-only tools stay report-only:** `/context-health` and `/cadence-audit` must never edit
  files. Don't add write behavior to them.

---

## The work — 8 fixes, in this order

### 🔴 MAJOR — these break the kit's headline contract ("green in one `/init-cadence` pass + one `/context-health` pass")

**FIX 1 — init-cadence must stamp `Last verified:` with a SHA, not a date.**
- Files: `.claude/skills/init-cadence/SKILL.md` (~step 4, line ~60),
  `.claude/commands/context-health.md`, `.claude/templates/module-claude-md.md`.
- Problem: SKILL.md step 4 stamps *today's date*. `/context-health` computes staleness with
  `git log --since="<date> 00:00:00"`. On an active repo, today-dated commits under a module's glob
  make the freshly-written note read 🔴 **stale on the very first audit** — the cleanup init promises
  won't exist. A date stamp can't encode write-time, so this is structurally unwinnable.
- Fix: have init step 4 stamp `Last verified:` with the **HEAD SHA** it verified against (the marker
  format already permits a SHA — see `module-claude-md.md` and `context-health.md`). Then
  `/context-health` resolves staleness via `SHA..HEAD`, which returns nothing for a note written
  against current HEAD → deterministically green. If you keep a human-readable date alongside, the
  SHA must be the authoritative field the audit uses.
- Done when: a module note written by init against current HEAD reads 🟢 on an immediate
  `/context-health`, even if HEAD was committed today. Keep marker format byte-identical across the
  two files (see never-do).

**FIX 2 — `/context-health` must not false-flag per-package monorepo shims.**
- Files: `.claude/commands/context-health.md` (steps 1 and 5), cross-check
  `.claude/skills/init-cadence/SKILL.md` (~line 81, "run the cadence per package").
- Problem: init supports a per-package layout where each package gets its own *root-style* markerless
  `CLAUDE.md` (`@AGENTS.md` shim). Run from the monorepo root, context-health step 5 treats
  `packages/foo/CLAUDE.md` as a broken per-module note (no `Describes:`/`Last verified:`) → false 🟡
  Broken, and sprays false 🟠 Missing across that package's subdirs.
- Fix: teach steps 1/5 to recognize a **per-package sub-root** — a `CLAUDE.md` that `@import`s an
  `AGENTS.md` in its own directory (or has no `Describes:` marker and sits beside a package
  manifest). Exclude it from the markers check and treat its directory as a sub-root for steps 4–5.
  Keep the rule lean — one short paragraph, not a framework.
- Done when: a monorepo with `packages/*/CLAUDE.md` shims audits green from the root.

**FIX 3 — global install (`~/.claude/`) needs the same protection the repo path already has.**
- Files: `README.md` (global-install paragraph ~22-29; cross-check the "slash commands not showing
  up?" callout ~63-67), `.claude/guide/context.md` (Delivery modes ~9-24); optionally
  `.claude/skills/init-cadence/SKILL.md`.
- Problem: the global-install instructions carry **no** merge-not-replace warning, **no** safe-copy
  command, and **no** `~/.claude/.claude/` nesting check — yet `~/.claude/` is the one place
  guaranteed to already hold `settings.json`, MCP config, and global commands. A Finder "Replace" or
  `cp -R kit/.claude ~/.claude/` destroys config for *every* repo at once. The repo path
  (`<your-repo>/.claude/`) already documents all of this; the higher-stakes path is left bare.
- Fix: in the global paragraph and guide Delivery modes, reuse step 1's merge-not-replace wording and
  a concrete safe-copy command targeted at `~/.claude/` — `cp -R <kit>/.claude/. ~/.claude/` (note
  trailing `/.`) or `rsync -a <kit>/.claude/ ~/.claude/`, plus a "never Finder-Replace `~/.claude/`"
  caution. Add `~/.claude/.claude/` to the "commands not showing up?" callout (you copied a level too
  deep — flatten its contents up). Optionally mirror init-cadence's nested-kit check for global mode.
  **Reuse existing wording** — don't invent a divergent third phrasing (see FIX 8).
- Done when: both install paths carry equal-strength merge protection, and the global-nesting failure
  mode has a documented symptom + recovery.

### 🟡 MINOR / NIT

**FIX 4 — dangling root `CLAUDE.md` + false dogfooding claim. (Decision pre-made: option A.)**
- Files: `README.md` (line ~199), `AGENTS.md` (lines ~3-4), `.gitignore` (line 4).
- Problem: `README.md:199` links `[CLAUDE.md](CLAUDE.md)` → 404. `AGENTS.md:3-4` claims this repo
  loads `AGENTS.md` via a `CLAUDE.md` `@AGENTS.md` shim that doesn't exist. `.gitignore:4` is a bare
  `CLAUDE.md` rule that would swallow even a generated one — the exact anti-pattern the kit's own
  `guide/context.md` warns against.
- **Chosen route (A):** make the master repo dogfood the mechanism. Add a tracked root `CLAUDE.md`
  generated from `.claude/templates/root-CLAUDE.md` (must contain the `@AGENTS.md` line), and change
  `.gitignore:4` from bare `CLAUDE.md` to `/CLAUDE.md` so the root shim tracks while keeping nested
  notes ignorable. (Alternative (B), only if the reviewer overrides: drop the `[CLAUDE.md](CLAUDE.md)`
  link in `README.md:199` and reword `AGENTS.md:3-4` so neither references a nonexistent file.)
- Done when: no dangling link, and AGENTS.md's claim is true for this repo. Note the chosen route in
  the report.

**FIX 5 — staleness check misses commits for mid-segment-wildcard globs.**
- Files: `.claude/commands/context-health.md` (step 3), `.claude/templates/module-claude-md.md`
  (and/or the guide).
- Problem: a `Describes:` glob like `src/**/*.ts` silently misses top-level files — git's default
  pathspec `**` doesn't match zero directories (reproduced: `src/auth/**/*.go` returns empty for a
  top-level `src/auth/top.go`). Note reads 🟢 while code moved — the exact false-negative the cadence
  exists to prevent.
- Fix: wrap the glob with literal-glob pathspec magic in **both** the `--since` and the `SHA..HEAD`
  variants: `git log ... -- ':(glob)<Describes glob>'`. Add one line to the template/guide steering
  `Describes:` toward directory-prefix globs (`src/auth/**`) or noting that `**/*.ext` needs
  `:(glob)`.
- Done when: the `:(glob)` form is used in step 3 and the steering note exists. (Couples with FIX 1 —
  both fix the same staleness query; do them together.)

**FIX 6 — `(confirm` grep over-matches ordinary prose.**
- Files: `.claude/commands/context-health.md` (step 6, the grep ~line 56).
- Problem: `grep -inE '\(\**confirm'` flags `(confirm with the team)` and `(confirmation)` as 🟣
  Unverified — false positives on a healthy repo.
- Fix: require a closing boundary, bold-tolerant: `grep -inE '\(\**confirm\**[:)]'` (still catches
  `**(confirm: …)**` and `(**confirm**)`, skips prose). Update every place the pattern is quoted so
  they stay in sync.
- Done when: the tightened pattern is used consistently wherever the `(confirm` grep is documented.

**FIX 7 (nit) — guard the no-git path in `/context-health`.**
- Files: `.claude/commands/context-health.md` (top of step 3).
- Problem: step 3 runs `git log` unconditionally; the skip-if-no-git rule lives only in Notes, so an
  agent can hit `fatal: not a git repository` before reaching it.
- Fix: add one precondition line atop step 3 — "If `git rev-parse --git-dir` fails, skip this step
  (no git → can't compute staleness; see Notes) and proceed to steps 4-6," mirroring
  `init-cadence/SKILL.md`'s existing guard.
- Done when: step 3 self-guards.

**FIX 8 (lean) — de-duplicate the README install story.**
- Files: `README.md`.
- Problem: the global-install path is told 2-4× and the safe-copy/merge caveat 2× in slightly
  divergent wording (already drifting). Bloats the install front door.
- Fix: state the safe-copy command + trailing-`/.`/merge caveat **once** in step 1; have "Updating
  the cadence" point back to it instead of reprinting. Collapse the global-install explanation to the
  "Two ways to install" block with one-clause pointers elsewhere; let `guide/context.md` Delivery
  modes remain the single deep reference (link to it, don't re-narrate). **Do FIX 3 and FIX 8
  together** so you converge on one canonical phrasing rather than fighting each other.

---

## Bonus (NOT from the audit — surfaced separately; do only if quick)
The named-workflow invocation `Workflow({name: "cadence-audit"})` **fails on CRLF checkouts**:
`.claude/workflows/cadence-audit.js` has CRLF line endings, and the approval-dialog validator rejects
carriage returns as hidden control characters. Anyone running the workflow by name on a CRLF checkout
hits this wall. Consider adding a `.gitattributes` pinning the kit to LF — e.g.
`.claude/workflows/*.js text eol=lf` (or `* text=auto eol=lf` repo-wide) — and normalizing existing
files. Flag it in the report; don't let it expand scope.

## Definition of done (gate before reporting)
- [ ] Plan written to `plans/cadence-audit-fixes.md`; each fix is a small, self-contained diff.
- [ ] **Marker byte-check:** `Describes:`/`Last verified:` labels still byte-identical between
      `module-claude-md.md` and `context-health.md` (diff the label bytes).
- [ ] **Cross-reference pass:** every path/command/heading you touched still resolves; no new dangling
      links.
- [ ] `/context-health` on *this* repo is green (or any residual is explained).
- [ ] Spot re-verify the 3 majors' mechanics: simulate a same-day-commit module (FIX 1 → green), a
      `src/x/**/*.ext` glob with a top-level file (FIX 5 → commit now surfaces), and confirm the
      global-install paragraph carries the merge warning (FIX 3).
- [ ] Optionally re-run `/cadence-audit` to confirm the verdict moved toward `bulletproof`.

## Report to the reviewer chat — use exactly this format
Post a single message to the reviewer chat:

> **Cadence-audit fixes — review request**
> **Branch:** `<branch>` · **Plan:** `plans/cadence-audit-fixes.md`
>
> **Status table** — one row per fix (1-8 + bonus):
>
> | # | Finding (1-line) | Severity | Status | Files changed |
> |---|---|---|---|---|
> | … | … | major/minor/nit | ✅ fixed / ⏭️ deferred / ❓ needs-decision | … |
>
> **Per-fix detail** (only for non-trivial ones): what the diff does, and *why* if you deviated from
> the prescription.
> **Decision made:** FIX 4 → route (A) shipped a tracked root CLAUDE.md (or note an override to B).
> **Gate results:** marker byte-check ✅/❌ · cross-ref pass ✅/❌ · `/context-health` green ✅/❌ ·
> majors spot-verified ✅/❌ · (cadence-audit re-run verdict, if done).
> **Residual risks / out-of-scope:** anything you intentionally left (e.g. the CRLF `.gitattributes`
> if deferred), with a one-line reason.
> **Suggested review focus:** the 2-3 diffs most worth a careful look (the majors + any
> marker-touching edit).

Keep the report skimmable — a reviewer should be able to approve from the table + gate results and
spot-check only the flagged diffs. Do not mark a fix ✅ unless its "Done when" holds.
