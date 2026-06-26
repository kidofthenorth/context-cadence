# Plan: declare module-context strategy (stop false 🟠 on docs-routed repos)

> One change per plan. Docs-and-skill change, no application code.

## Goal
context-health hard-codes "module context = nested `CLAUDE.md`", so a repo that *deliberately* routes
module context through tracked docs (e.g. `docs/ENGINEERING.md` governed by `check-docs.mjs`) gets a
permanent, meaningless 🟠 *missing* on every run. Let a repo **declare** its strategy in root
`AGENTS.md` (`Module context: docs — …`); context-health respects it (one ℹ️ note, no 🟠), init-cadence
writes it when context is routed elsewhere, and the guide defines it once. Default (absent / `nested`)
is unchanged.

## Context check (against the real files)
- [x] context-health step 4 + 🟠 output are the hard-coded spots; init-cadence step 4 handles
      module notes (tiny-repo skip only — no "routed elsewhere" branch today).
- [x] New convention `Module context:` is defined **once** in guide/context.md and referenced from
      both command + skill (same discipline as `(confirm)`). Marker format untouched → no never-do tripped.
- [x] Surfaced by the munity-full-stack run: it improvised "keep docs/" but left no machine-readable
      declaration, so context-health 🟠s forever. This makes that choice first-class.

## In scope
- `.claude/guide/context.md` — canonical definition of the `Module context:` declaration + the tradeoff.
- `.claude/commands/context-health.md` — step 4 reads the declaration; 🟠 only when nested; add ℹ️ line.
- `.claude/skills/init-cadence/SKILL.md` — step 4: when context is routed elsewhere, write the
  declaration instead of a competing nested surface.
- `.claude/templates/root-AGENTS.md` — optional one-line hint (discoverable for manual/non-Claude users).
- `.claude/VERSION` — bump per the payload-change policy.

## Out of scope
- The `Describes:`/`Last verified:` marker format.
- Auto-adding the declaration to any *target* repo (that's a one-line offer, not part of this change).
- Forbidding nested notes when `docs` is declared — they stay allowed as a complement.

## Steps
1. guide: add *When a repo already routes module context elsewhere* — the `Module context:` line,
   what context-health does with it, and the honest auto-load tradeoff.
2. context-health: step 4 checks root `AGENTS.md`/`CLAUDE.md` for `Module context:`; if routed
   elsewhere, no 🟠 — emit one **ℹ️** note (audit can't see docs/; freshness rides on its own checker).
3. init-cadence: step 4 sub-bullet — if the repo already routes context via governed docs, write the
   declaration in AGENTS.md rather than a parallel nested surface; offer nested only as a complement.
4. template: optional `Module context:` hint line.
5. Bump VERSION; rebuild the distributable zip.

## Definition of done
- [ ] A repo with `Module context: docs` gets ℹ️ (not 🟠) from context-health; default unchanged.
- [ ] `Module context:` defined once (guide), referenced by command + skill; marker format intact.
- [ ] init-cadence writes the declaration for docs-routed repos. VERSION bumped, zip rebuilt.

## Constraints
- Add paragraphs, not a framework. Don't undermine nested-CLAUDE auto-load as the default; just stop
  punishing a deliberate, governed alternative.

## Progress
- Done: steps 1–5 implemented + verified — `Module context:` defined once in guide, referenced by
  command + skill + template; marker format intact; VERSION 0.4.4; zip rebuilt.
- Next: fresh-session review; offer to add the one `Module context: docs — …` line to
  munity-full-stack's `AGENTS.md` so its permanent 🟠 turns ℹ️/green.

## Review lenses (fresh session)
- [ ] Correctness: context-health suppresses 🟠 only when a declaration is actually present.
- [ ] Consistency: convention defined once; command + skill + guide agree; default behavior unchanged.
- [ ] Edge cases: no declaration (default 🟠), `nested` explicit, `docs` with/without checker, tiny repo.
