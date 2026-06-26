# Plan: pre-ship gate — cohesion UX + non-AI reach

> User asked, before shipping: (1) confirm the cadence setup is cohesive for a good user
> experience, and (2) confirm it extends outside AI usage for users who don't use AI.
> Run as a multi-agent workflow (6 finders across 2 lenses → adversarial verify → synthesize),
> then clear the verified tail in one diff. One round, small diffs.

## Method
Ran the `cadence-ship-gate` workflow: finders traced onboarding, the work loop, cross-doc
consistency, the update path (cohesion lens) plus non-AI support and non-AI gaps (reach lens);
each problem finding was adversarially verified against the real text. The run hit a session
limit at the end — both synthesizers and all 7 non-AI verifiers died — so the cohesion lens is
fully agent-verified and the non-AI lens was verified by hand (reading the files directly) and
synthesized in-loop.

## Q1 — Is it cohesive for good UX? **Yes (zero majors).**
9 findings survived verification: 1 minor, 8 nits, against 18 confirmed strengths. No blockers.
Matches the prior audit conclusion (bulletproof on substance; polish tail is inherent).

## Q2 — Does it extend to non-AI users? **In substance yes; signposting was the gap.**
- The system already extends to humans by design: the AGENTS.md template says "Claude, Codex,
  Cursor, **or a human**" (`templates/root-AGENTS.md`), the guide says humans read it and follow
  the loop by hand and documents a by-hand bootstrap (`guide/context.md`), and `plan.md` +
  `checklists/review.md` are fill-in-by-hand.
- The gap was on the page, not in the substance: the README headline ("teaches any coding AI"),
  the all-`/init-cadence` setup steps, and the "by hand" carve-outs addressed only to *other AI
  tools* never spoke to a no-AI reader. Rejected one finder claim ("CLAUDE.md is noise to a
  human") — it's correct separation of concerns, inert not defective.
- **Fix:** added a "Not using AI at all?" README callout pointing at the human-readable AGENTS.md
  and the guide's by-hand setup. Q2 is now true on the page, not only in substance.

## Tail cleared (this round's diff)
Payload (triggered the bump):
- `.claude/skills/init-cadence/SKILL.md` — closing report now recommends `/context-health` **then
  `/plan`** (matches the command file's promise; was the only *minor*).
- `.claude/commands/review.md` — acknowledges checklist **Pass 4** (Scope & diff hygiene) has no
  argument and is a manual pass, so command and checklist no longer silently disagree.

Root docs / README (not payload):
- `README.md` — `docs`→`guide` in the bump-rule slot (now identical to VERSION + step 2); stamp
  example → placeholder `cadence: <x.y.z>` (can't drift again); surfaced `/cadence-audit` + its
  orchestration requirement in the loop narrative; update-step copy command regained the trailing
  `/.` note + same-named-overwrite caveat + `<your-repo>` placeholder consistency; **added the
  "Not using AI at all?" callout** (Q2).
- `AGENTS.md` — stamp example → placeholder `cadence: <x.y.z>`.
- `CLAUDE.md` — active-plan pointer updated to this plan.

Version / artifact:
- `.claude/VERSION` 0.4.9 → **0.4.10** (payload changed: SKILL + command).
- `context-loop-cadence-0.4.9.zip` → `context-loop-cadence-0.4.10.zip`.

## Out of scope (did NOT touch)
- The `Describes:` / `Last verified:` marker format (unchanged).
- The README headline ("teaches any coding AI") — the new callout addresses the no-AI reader
  without diluting the primary hook.
- Two findings left as-is by judgment (both nits): the nested-`.claude/.claude/` copy mistake
  (init already auto-flattens it silently; warning the README would bloat the densest section) and
  a standalone `/cadence-audit` Workflow-tool note in the manifest (already covered by the new loop
  sentence + the command's own opt-in line).

## Verification
- [x] Cross-reference pass: every path/command referenced resolves.
- [x] Marker labels still byte-identical between `templates/module-claude-md.md` and
      `commands/context-health.md`.
- [x] No live `0.4.x` stamp literals remain in README/AGENTS.md (placeholders now); remaining
      `0.4.x` strings are historical (plans + the CLAUDE.md prior-round note).
- [x] No text implies non-Claude tools run slash commands natively.
- [x] Stamp and zip agree on 0.4.10; no `.DS_Store` in the payload or zip.

## Definition of done
- [x] Both questions answered with grounded evidence.
- [x] Verified nit tail cleared in one diff; judgment calls recorded.
- [x] VERSION bumped, zip reissued, active-plan pointer updated.
- [x] Verification pass green.
