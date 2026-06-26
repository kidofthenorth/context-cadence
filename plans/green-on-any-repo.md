# Plan: green on any repo via copy-paste + /init-cadence

> One change per plan. Docs-and-skill change, no application code.

## Goal
Make the spec true: drop `.claude/` into *any* repo, run `/init-cadence`, get correct context with
no per-repo cleanup. Two real gaps close it — (1) init-cadence output must be **correct by
construction**, not guessed; (2) the kit must be **immune to any host `.gitignore`**. The generated
`CLAUDE.md` collision (can't be renamed — Claude requires that name) is resolved **in-pass by asking
the user inline** and applying a one-line fix only with consent — no deferred handoff. Setup
questions stay (healthy, not guesses).

## Context check (done against the real files)
- [x] `.claude/docs/` is referenced in 10 places across 5 files (AGENTS, README×3, init-cadence×4,
      context-loop×2, context-health×1). Rename touches all.
- [x] Empirically tested: a host `.gitignore` with `docs/` ignores `.claude/docs/`; renaming to
      `guide/` is safe with no negation hack (a `.claude/.gitignore` negation is unreliable due to
      git's "can't re-include under an excluded dir" rule). → rename, don't ship a negation file.
- [x] After rename, no kit folder matches a common ignore pattern (`skills/ commands/ templates/
      checklists/ guide/` are all uncommon). File-level collisions (`*.md`, `VERSION`) are exotic.
- [x] `context.md`'s internal link `../commands/context-health.md` stays valid (guide/ is still a
      sibling of commands/). Marker format untouched.

## In scope
- Rename `.claude/docs/` → `.claude/guide/`; update all 10 references.
- `.claude/skills/init-cadence/SKILL.md` — verify-then-write (step 4 + guardrail) + stop-and-report (step 6).
- `.claude/guide/context.md` — update *Tracked vs gitignored* to stop-and-report; note collision-safe naming.
- `.claude/VERSION` — bump per the payload-change policy.

## Out of scope
- The `Describes:`/`Last verified:` marker format.
- Auto-editing any target repo's `.gitignore` (owner declined; init-cadence reports, never edits).
- Renaming `.claude/` itself (Claude Code hardcodes the path) or the `CLAUDE.md` context name.
- A `.claude/.gitignore` negation file (tested unreliable).

## Steps (each = one small, reviewable diff)
1. `mv .claude/docs .claude/guide`; update the 10 references across AGENTS/README/SKILLs/command.
2. init-cadence **verify-then-write**: every greppable claim (symbols, routes, paths, services,
   commands) confirmed against source *before* writing; `(confirm)` reserved for genuine
   non-verifiable judgment calls only. Rewrite step 4 + the `(confirm)` guardrail.
3. init-cadence **ask-in-pass**: step 6 detects a host rule that would ignore generated context,
   asks the user inline (keep local vs a one-line fix), applies the fix only with consent, and
   resolves it within the pass — no deferred handoff, no `.gitignore` edit without say-so.
4. `guide/context.md`: *Tracked vs gitignored* reflects stop-and-report + collision-safe naming.
5. Bump `.claude/VERSION`.
6. Verify: every reference resolves to `.claude/guide/`; marker format intact; rebuild the zip.

## Definition of done
- [ ] init-cadence verifies checkable facts before writing; `(confirm)` only for judgment calls.
- [ ] `.claude/guide/` replaces `.claude/docs/`; all 10 references updated; no dangling `.claude/docs`.
- [ ] init-cadence stops-and-reports on a swallowing `.gitignore` rule; never auto-edits.
- [ ] Marker format byte-identical (template vs context-health). Zip rebuilt at the new VERSION.
- [ ] No changes outside "In scope".

## Constraints
- Add paragraphs, not a framework. Refine the seed files; don't gut them.
- Don't promise zero human input — setup questions remain; only *wrong guesses* are eliminated.

## Progress
- Done: steps 1–6 implemented this session — `docs/`→`guide/` rename (all refs), init-cadence
  verify-then-write + ask-in-pass, `(confirm)` demoted to a non-interactive fallback, guide doc +
  command + README aligned, VERSION 0.4.3.
- **Refined mid-build (user):** the gitignore fallback is *ask the user inline and resolve within the
  pass* (apply a one-line fix with consent), not a deferred report/handoff. The whole point: one
  init-cadence pass + one context-health pass, both green, nothing left over.
- Next: fresh-session review.

## Review lenses (fresh session)
- [ ] Correctness: verify-then-write instruction matches what an agent can actually grep.
- [ ] Consistency: no `.claude/docs` references remain; marker format intact; VERSION bumped.
- [ ] Edge cases: host ignores `docs/`, host ignores `CLAUDE.md`, repo with no git, tiny repo.
