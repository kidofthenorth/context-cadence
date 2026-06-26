# Plan: close the cadence gaps the ibkr-bot run exposed by improvisation

> One change per plan. Docs-and-skill change, no application code (this repo has none).

## Goal
The `/init-cadence` + `/context-health` run on ibkr-bot only caught three real problems because
the agent improvised beyond what the cadence instructs. Encode those so the *next* run handles
them by instruction, not luck: (1) `/init-cadence` detects context files a `.gitignore` rule
silently hides; (2) the `(confirm)` tag gains a documented lifecycle and `/context-health` flags
unretired ones; (3) `/context-health` documents the same-day day-granular false positive and how
to dismiss it.

## Context check (done first, against the real files)
- [x] All target files exist; only the new plan file is new.
- [x] `(confirm)` lives **only** in `init-cadence/SKILL.md:64` — not in the template, command, or
      docs; nothing reads or retires it. Confirmed gap.
- [x] `docs/context.md` takes **no** stance on tracked-vs-gitignored context. Confirmed gap.
- [x] `context-health.md` step 3 uses day-granular `--since`; no same-day rule documented.
- [x] `Describes:` / `Last verified:` marker format is **not** touched by this change — no
      never-do triggered. `(confirm)` is a separate inline convention, given a single canonical
      home (`docs/context.md`) and referenced elsewhere, mirroring the marker-sync discipline.
- [x] No git history in this workspace → cross-reference verification only, no git staleness check.

## In scope
- `.claude/docs/context.md` — canonical `(confirm)` lifecycle + tracked-vs-gitignored stance + sync the "Keeping it fresh" enumeration
- `.claude/commands/context-health.md` — new 🟣 Unverified finding + same-day Notes rule
- `.claude/skills/init-cadence/SKILL.md` — gitignore-detection in the Report step + point `(confirm)` at its canonical def
- `.claude/VERSION` — bump 0.4.0 → 0.4.1 (cadence tooling changed; the stamp must not lie)
- `CLAUDE.md` — point the active-plan line at this file

## Out of scope (do NOT touch)
- The `Describes:` / `Last verified:` marker format in `module-claude-md.md` / `context-health.md`.
- Adding a SHA-stamp nudge to `/init-cadence` (decided against — Notes-only keeps it lean; the
  SHA escape hatch already exists in the format).
- README finding-category enumeration (README doesn't enumerate them; nothing to sync).
- The completed `cadence-version-codex-tiny-repos.md` work.
- Any ibkr-bot file (that's the handoff, not this repo).

## Steps (each = one small, reviewable diff)
1. `docs/context.md`: add **Tracked vs gitignored** stance + a **Draft vs verified — the `(confirm)`
   tag** subsection (canonical definition + retirement step) + extend the "Keeping it fresh"
   enumeration with **unverified**.
2. `context-health.md`: add detection step 6 (scan for `(confirm)` tags), a 🟣 **Unverified** output
   category (ordered after 🟡 Broken), and two Notes — the `(confirm)` semantic caveat and the
   same-day day-granular dismissal rule.
3. `init-cadence/SKILL.md`: add a **Check tracking** bullet to the Report step (`git check-ignore`,
   surface tracked-vs-ignored, never edit `.gitignore`), and point the `(confirm)` guardrail at the
   canonical def in `docs/context.md`.
4. Bump `.claude/VERSION` to `cadence: 0.4.1`.
5. Point `CLAUDE.md`'s active-plan line at this file.
6. Verification pass (below).

## Definition of done
- [x] `/init-cadence` instructs a tracked-vs-gitignored check and never edits `.gitignore`.
- [x] `(confirm)` has one canonical definition + a retirement step; skill and command reference it.
- [x] `/context-health` flags unretired `(confirm)` tags (🟣 Unverified) and documents the same-day rule.
- [x] `docs/context.md` "Keeping it fresh" enumeration lists all four categories.
- [x] Marker format byte-identical between template and command; `(confirm)` defined in one place.
- [x] VERSION bumped; active-plan line updated. No changes outside "In scope".

## Constraints
- Add paragraphs, not a framework. Refine the seed files; don't gut them.
- Single source of truth for the new `(confirm)` convention — define once, reference elsewhere.

## Progress
- Done: context check passed; steps 1–6 implemented this session; verification pass complete.
- **Real-world refinement (from validating against ibkr-bot):** the `(confirm)` tags in the wild
  use a *family* — bare `(confirm)` **and** detailed `(confirm exact names)` / `(confirm function
  name)`. A naive `grep "(confirm)"` misses the detailed form (it tripped my own grep). So the
  detection in step 2 now matches the **opening `(confirm`** (case-insensitive); docs + skill say
  the tag may name what to check. This is what makes the audit actually catch them.
- Files modified: `.claude/docs/context.md`, `.claude/commands/context-health.md`,
  `.claude/skills/init-cadence/SKILL.md`, `.claude/VERSION`, `CLAUDE.md`, this plan file.
- Next: none — validated end-to-end on ibkr-bot (0.4.1 audit ran clean pre- and post-fix). A truly
  fresh-session pass is optional, not blocking.

## Review lenses
- [x] Correctness: ran the actual `(confirm` grep against ibkr — confirmed behavior and *refined*
  the pattern after finding the markup-inside `(**confirm` variant a naive grep missed.
- [x] Consistency: marker format untouched; `(confirm)` defined once in `docs/context.md`;
  enumerations in docs + command agree.
- [x] Edge cases exercised on real repos: ibkr has git (staleness path), cadence has none (no-git
  path), post-fix ibkr has zero `(confirm)` tags (clean path). Public-repo-wants-ignored = the
  `.gitignore` tracking call, documented and left to the owner.
