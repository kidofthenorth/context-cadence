# Plan: correctness review of the `.claude/` doc set + version semantics

> Fresh-eyes correctness pass over the cadence doc set (the first since it only ever had a
> cross-reference check), plus the three small follow-ups that pass surfaced. One round, small diffs.

## Goal
Confirm the `.claude/` docs are internally correct (not just cross-referenced), close the one real
gap — undefined `.claude/VERSION` bump semantics — and record the deliberate "leave as-is" calls on
the gitignore-prominence and `(confirm)`-authoring questions.

## Correctness review — findings (lens: correctness only)
Reviewed the full `.claude/` doc set as it stands. **Verdict: clean.** The four load-bearing
invariants all hold:

1. **Step-6 `(confirm)` grep matches every authored form — PASS (the hardest check).**
   `grep -inE '\(\**confirm'` catches every form the docs tell authors to write — bare `(confirm)`,
   detailed `(confirm exact names)` / `(confirm exact route list)`, canonical `**(confirm: route
   set)**` — *and* the warned-against bold-inside `(**confirm…**)`. Verified empirically: the same
   regex matched every live `(confirm` occurrence across the docs. The over-match (it would also
   flag real prose containing "(confirm") is the safe direction — better to over-flag than miss.
2. **§Draft vs verified matches behavior.** `context.md` says `/init-cadence` tags inferences with
   `(confirm)`, stamps today's date, and `/context-health` flags unretired tags as 🟣 Unverified —
   which is exactly what `init-cadence/SKILL.md` (steps 4 + guardrail) and `context-health.md`
   (step 6 + output) actually instruct.
3. **§Tracked vs gitignored matches behavior.** `context.md` says `/init-cadence` surfaces which
   written files are ignored and never edits `.gitignore`; `init-cadence/SKILL.md`'s "Check
   tracking" sub-step does exactly that (`git check-ignore` / read `.gitignore`, surface as an
   explicit decision, never edit). The "bare `CLAUDE.md` matches at every depth → portable
   `AGENTS.md` survives but the whole `CLAUDE.md` hierarchy goes local-only" claim is accurate.
4. **Marker labels byte-identical.** `Describes:` / `Last verified:` match between
   `templates/module-claude-md.md` and `commands/context-health.md`. No native-slash-command
   overclaims anywhere (README, root-AGENTS.md template, context.md all honest).

Minor observations (none are correctness bugs):
- **Recommended `(confirm)` micro-form is inconsistent.** `context.md` uses a colon
  (`**(confirm: route set)**`); `init-cadence/SKILL.md` uses a space (`**(confirm exact route
  list)**`). Both grep cleanly, so the audit is unaffected — authors just see two canonical shapes.
- **`.claude/.DS_Store` rode along in the payload** — "copy this one folder" carried macOS cruft
  into every target repo. **Actioned:** deleted both `.DS_Store` files, added a root `.gitignore`
  and a payload-level `.claude/.gitignore` (so the guard travels with the folder), then — because
  adding a payload file trips the bump rule above — bumped the stamp to **0.4.2** and reissued the
  release zip as `context-loop-cadence-0.4.2.zip`.

## Decisions on the follow-ups
- **Version semantics (Task 2) — FIX.** Added the bump rule where the stamp is documented: a
  comment line in `.claude/VERSION` and a sentence in README §Updating step 1. Rule: *bump on any
  change to the `.claude/` payload (skills, commands, templates, docs, checklists); project notes
  aren't part of the stamp.* This makes "compare the stamps" mean something — equal stamps now
  imply identical tooling.
- **Gitignore prominence (Task 3) — CONFIRM, no edit.** The consequence is already prominent: a
  dedicated `### Tracked vs gitignored` subsection in `context.md` with the payload in **bold**, and
  a dedicated bolded "**Check tracking.**" sub-step in `init-cadence/SKILL.md` that says "don't bury
  it." Strengthening further would over-weight one detail in a docs-only repo.
- **`(confirm)` authoring footgun (Task 4) — CONFIRM, no edit.** `context.md` already gives both a
  do and a don't with examples ("write `**(confirm: route set)**`, not `(**confirm…**)`") and the
  audit tolerates the bad form, so nothing slips. The colon-vs-space inconsistency above is trivial
  and harmonizing it isn't worth a diff; left as a noted observation.

## In scope (files this round may touch)
- `.claude/VERSION` (add bump-rule comment) — done
- `README.md` (define stamp semantics in §Updating) — done
- `plans/correctness-review-and-version-semantics.md` (this file) — done
- `plans/cadence-version-codex-tiny-repos.md` (Progress checkpoint) — done
- `.gitignore` + `.claude/.gitignore` (new — `.DS_Store` guard); deleted `./.DS_Store` and
  `./.claude/.DS_Store` — done
- `.claude/VERSION` 0.4.1 → 0.4.2 (payload changed); README/AGENTS.md format examples bumped to
  match; `context-loop-cadence-0.4.1.zip` → `context-loop-cadence-0.4.2.zip` — done

## Out of scope (do NOT touch)
- The `Describes:` / `Last verified:` marker format (both files or none — unchanged this round).
- The PreToolUse hook / `settings.json` idea.
- CI, changelogs, version-diff tooling (noted, not actioned).
- Re-flowing the `(confirm)` examples to a single micro-form (judged not worth a diff).

## Verification
- Re-ran the cross-reference pass: every path referenced in README / templates / skills / commands
  resolves. `.claude/VERSION` still appears everywhere it's referenced.
- Marker labels still byte-identical between template and command.
- No text implies non-Claude tools run slash commands/skills natively.

## Definition of done
- [x] Correctness lens run over the full `.claude/` doc set; findings recorded here.
- [x] `.claude/VERSION` bump rule defined in README and the VERSION file.
- [x] Gitignore-prominence and `(confirm)`-authoring calls recorded with rationale.
- [x] Verification pass re-run; markers in sync; no native-command overclaims.
- [x] No changes outside "In scope".
