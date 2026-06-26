# Plan: Global-skill delivery mode (the middle path)

> One change per plan. One plan per session. Keep the diff small enough to review in one sitting.

## Goal
Let the cadence be installed **once globally** (`~/.claude/`) and invoked against any repo without
copying — while keeping the existing **copied `.claude/` folder** as the portable, team default.
Same payload, same templates; two delivery modes. "Done" = a solo user can `cp` the kit into
`~/.claude/` once and run `/init-cadence`, `/plan`, `/review` in a repo that has **no** `.claude/`,
and every engine file finds its templates/guide/checklist correctly.

## Context check (done — findings below)
Verified against the real files via an Explore pass + direct reads. Findings:
- **The engine files are agent *instructions*, not executable code.** "Resolve paths in both modes"
  means making the *instruction text* mode-aware, not writing path-discovery logic. Only
  `workflows/cadence-audit.js` is real code.
- **Global mode falls out as a destination change**, not new mechanics: copying the kit's `.claude/`
  *contents* into `~/.claude/` mirrors the repo layout exactly (`~/.claude/templates/`, `~/.claude/guide/`,
  `~/.claude/skills/`, …). So the engine root is just "`<repo>/.claude/` if present, else `~/.claude/`."
- **Functional reads that must resolve in global mode:** `init-cadence` (presence check + template
  reads, SKILL.md lines 27-28/45/51/54), `context-loop` (`templates/plan.md` line 11, `checklists/review.md`
  line 24), `/plan` cmd (line 10), `/review` cmd (line 14). Everything else is narrative pointers.
- **`cadence-audit.js` is OUT of scope:** it audits *this* master repo's kit via a `root` arg
  defaulting to `.`; it isn't run against target repos. Its marker-format never-do is unaffected
  (both files stay in the engine wherever it lives).
- **Precedence:** when a repo has its own `.claude/`, it wins over `~/.claude/` (matches Claude Code's
  project-over-user merge). State this so the two modes can't fight.
- **VERSION:** stays the payload stamp. In global mode it lives at `~/.claude/VERSION` and governs all
  repos at once — a doc note, not a semantic change. Bump 0.4.10 → 0.4.11 (payload changed).

## In scope (files this change is allowed to touch)
- `.claude/guide/context.md` — add the canonical "Delivery modes" + engine-root resolution rule (single source of truth)
- `.claude/skills/init-cadence/SKILL.md` — mode-aware presence check + engine-root note
- `.claude/skills/context-loop/SKILL.md` — engine-root note (covers `templates/plan.md`, `checklists/review.md`)
- `.claude/commands/plan.md` — engine-root note for the `templates/plan.md` read
- `.claude/commands/review.md` — engine-root note for the `checklists/review.md` read
- `.claude/commands/context-health.md` — one-line mode note so template-marker refs resolve globally
- `README.md` — "Two ways to install" (copied default vs global solo) + global path in the Updating section
- `AGENTS.md` — Map/Project: note the engine can live in-repo (default) or `~/.claude/` (solo)
- `.claude/VERSION` — bump to 0.4.11

## Out of scope (do NOT touch)
- `.claude/workflows/cadence-audit.js` — audits the master kit; not invoked against target repos
- The marker format (`Describes:` / `Last verified:`) — never-do; must stay byte-identical across files
- `.claude/templates/plan.md`, `.claude/checklists/review.md` intent — refine wording only if needed, don't gut
- Copied-mode behavior — must remain exactly as today (additive change only)
- Building an actual installer/script — global install is a documented `cp`, not new tooling

## Steps (each step = one small, reviewable diff)
1. **Guide — define the modes once.** In `.claude/guide/context.md`, add a short "Delivery modes"
   subsection: (a) copied `.claude/` = default/portable/team; (b) global `~/.claude/` = solo/no-copy;
   (c) the **engine-root rule**: any cadence file that references `.claude/<X>` reads it from the
   engine install — the repo's `.claude/` if present, else `~/.claude/`; repo wins on conflict.
   This is what every other file points at.
2. **init-cadence SKILL.** Make "Before you start" presence check mode-aware (templates reachable in
   `<repo>/.claude/templates/` **or** `~/.claude/templates/`); add a one-line engine-root note so the
   step 2/3/4 template reads resolve in both modes. Keep it lean.
3. **context-loop SKILL.** Add the one-line engine-root note near the top (covers the `templates/plan.md`
   and `checklists/review.md` references already in the file — no need to edit each path string).
4. **plan + review commands.** Add the same one-line engine-root note to `commands/plan.md` and
   `commands/review.md` so their direct reads resolve when the engine is global.
5. **context-health command.** Add one short mode note so its template-marker references resolve from
   `~/.claude/` in global mode; confirm its repo-relative checks (nested-kit, repo context) are harmless
   when there's no `<repo>/.claude/`.
6. **README.** Add "Two ways to install" (copied = default/recommended for teams; global = solo
   convenience, copy contents into `~/.claude/`, commands available everywhere). Note global `~/.claude/VERSION`
   in the Updating section. Keep copied mode as the headline path.
7. **AGENTS.md.** One/two lines in Project/Map: engine can live in-repo (default) or `~/.claude/` (solo);
   generated artifacts (AGENTS.md/CLAUDE.md/plans/) always live in the repo.
8. **Bump `.claude/VERSION`** to `cadence: 0.4.11`.

## Definition of done
- [x] `.claude/guide/context.md` states the two modes + the engine-root resolution rule (incl. repo-wins precedence)
- [x] `/init-cadence` presence check passes when templates live only in `~/.claude/` (no repo `.claude/`)
- [x] context-loop / plan / review instructions resolve `templates/` and `checklists/` from the engine root in both modes
- [x] README documents both install modes; copied stays the recommended team default
- [x] AGENTS.md Map notes the two engine locations; artifacts stay repo-local
- [x] `.claude/VERSION` = `cadence: 0.4.11`
- [x] Verification (per AGENTS.md): cross-reference pass — every path/command referenced resolves;
      marker format still byte-identical between `templates/module-claude-md.md` and `commands/context-health.md`
- [x] No changes outside "In scope"

## Constraints
- Implement this plan exactly as written. Do not add anything not listed above. If something necessary is
  missing, stop and update this plan first.
- Preserve: copied-mode behavior unchanged; the marker-format never-do; the seed files' intent.
- Additive only — global mode is layered on; nothing about the copied flow regresses.

## Progress (checkpoint here before any context reset)
- Done so far: Phase 2 complete — all 8 steps implemented and verified (cross-ref pass clean, marker format byte-identical, VERSION bumped to 0.4.11).
- Next step: Phase 3 — fresh-session review (correctness, then security, then tests lenses).
- Files modified: `.claude/guide/context.md`, `.claude/skills/init-cadence/SKILL.md`, `.claude/skills/context-loop/SKILL.md`, `.claude/commands/init-cadence.md`, `.claude/commands/plan.md`, `.claude/commands/review.md`, `.claude/commands/context-health.md`, `README.md`, `AGENTS.md`, `.claude/VERSION`.

## Review lenses (run after implementation, each in a fresh session)
- [ ] Correctness — do both modes' instructions actually resolve to a real file? copied-mode untouched?
- [ ] Security — n/a (docs/instructions); confirm no guidance to weaken `.gitignore`/secrets handling
- [ ] Tests & edge cases — cross-reference pass; repo-has-both-`.claude/`s precedence; tiny-repo path still works
