# Plan: cadence version stamp, portable loop, and tiny-repo guidance

> One change per plan. This is a docs-and-template change for Opus to implement.

## Goal
Make the cadence easier to maintain across many repos by adding a copied version stamp, documenting the update path, making the non-Claude boundary honest, and giving tiny repos explicit scale-down guidance.

## Context check (Opus does this FIRST, before writing implementation changes)
Before implementing, verify this plan against the real codebase and report back:
- [ ] Confirm the files named below exist, except the new `.claude/VERSION`.
- [ ] Confirm `.claude/templates/module-claude-md.md` and `.claude/commands/context-health.md` still agree on the exact `Describes:` / `Last verified:` marker format.
- [ ] Confirm `/init-cadence` behavior still says existing `AGENTS.md` / `CLAUDE.md` are improved or approved before overwrite, not silently clobbered.
- [ ] Confirm whether this workspace has git history. If not, do the cross-reference pass without git-based staleness checks.
- [ ] List any assumption in this plan that turned out to be wrong.

Do not start implementing until these are confirmed or the plan is corrected.

## In scope (files this change is allowed to touch)
- `.claude/VERSION` (new)
- `README.md`
- `AGENTS.md`
- `.claude/templates/root-AGENTS.md`
- `.claude/templates/root-CLAUDE.md`
- `.claude/skills/init-cadence/SKILL.md`
- `.claude/skills/context-loop/SKILL.md`
- `.claude/docs/context.md`
- `.claude/commands/init-cadence.md`
- `.claude/commands/context-health.md` only if needed for wording/cross-reference, not marker format changes

## Out of scope (do NOT touch)
- The off-by-default PreToolUse hook / `settings.json` idea.
- CI integration, generated inventories, changelog files, version-diff tooling, or auto-update scripts.
- The `Describes:` / `Last verified:` marker format unless both `.claude/templates/module-claude-md.md` and `.claude/commands/context-health.md` are updated together and the user explicitly approves.
- Rewriting the seed plan/review files beyond small wording needed for this change.
- Application code, because this repo has none.

## Steps (each step = one small, reviewable diff)
1. **Add the cadence stamp.**
   - Create `.claude/VERSION`.
   - Use a single simple line, for example `cadence: 0.4.0`.
   - Add `.claude/VERSION` to the README's "What's in the folder" tree and plain-language file list.
   - Update root `AGENTS.md` map so this repo's own context knows the stamp is part of the copied payload.

2. **Document the update path.**
   - Add a short README section such as `## Updating the cadence in an existing repo`.
   - Explain the intended fleet workflow:
     - compare/read `.claude/VERSION`;
     - re-copy `.claude/` from this master repo into the target repo;
     - keep the target repo's root `AGENTS.md` and `CLAUDE.md` project-specific;
     - re-run `/init-cadence` only when project context needs merge/refinement;
     - run `/context-health` after refreshing the tooling.
   - Keep the claim conservative: re-copying `.claude/` refreshes the cadence tooling/templates; it should not imply target project notes are automatically correct after the copy.

3. **Make the Codex/Cursor story true in the portable baseline.**
   - Update `.claude/templates/root-AGENTS.md` with a short "How to work here" or "Agent loop" section in plain prose:
     - plan before code;
     - inspect real files before implementation;
     - make small diffs;
     - update context in the same diff as behavior changes;
     - use a fresh review pass, one lens at a time.
   - Make that section tool-agnostic: Codex/Cursor can follow it by hand, while Claude users can use `/plan`, `/review`, and `/context-health`.
   - Update `README.md` and `.claude/docs/context.md` to say the boundary honestly: `AGENTS.md` carries the portable baseline; Claude Code additionally gets slash commands, skills, and auto-loaded nested `CLAUDE.md` behavior.
   - Avoid marketing-y language that implies the slash-command loop runs natively in non-Claude tools.

4. **Add graceful scale-down guidance for tiny repos.**
   - Add one concise paragraph to `README.md` near setup or "How the notes work":
     - tiny repo or 3-file script: root `AGENTS.md` + root `CLAUDE.md` can be the whole cadence;
     - skip per-module notes and heavy planning until the repo grows;
     - still keep the basic loop: plan, small diff, verify.
   - Add matching guidance to `.claude/docs/context.md` under granularity.
   - Update `.claude/skills/init-cadence/SKILL.md` so `/init-cadence` explicitly reports "no major modules; root notes are enough for now" instead of making tiny repos feel incomplete.

5. **Refresh command/template wording where needed.**
   - Update `.claude/commands/init-cadence.md` if it needs to mention `.claude/VERSION` or tiny-repo behavior.
   - Update `.claude/skills/context-loop/SKILL.md` only if the portable/manual loop wording in `AGENTS.md` needs to align with the Claude-specific loop.
   - Keep the Claude-only details in `.claude/templates/root-CLAUDE.md`; do not move Claude-specific environment or slash-command mechanics into `AGENTS.md`.

6. **Run the repo's verification pass.**
   - Cross-reference every changed path/command named in README, templates, skills, and commands.
   - Confirm `.claude/VERSION` appears anywhere it is referenced.
   - Confirm the marker format remains identical between `.claude/templates/module-claude-md.md` and `.claude/commands/context-health.md`.
   - Confirm no text says non-Claude tools can run Claude slash commands/skills natively.

## Definition of done
- [ ] `.claude/VERSION` exists and is documented as part of the copied cadence folder.
- [ ] README has a clear update path for existing repos.
- [ ] The portable `AGENTS.md` template contains a manual, tool-agnostic version of the loop.
- [ ] README/context docs honestly distinguish portable `AGENTS.md` guidance from Claude-only slash commands, skills, and nested `CLAUDE.md` auto-loading.
- [ ] Tiny repos are explicitly allowed to use only root notes until they grow.
- [ ] `/init-cadence` instructions mention the tiny-repo scale-down behavior.
- [ ] Verification pass complete: every referenced path resolves, and the marker format stayed in sync.
- [ ] No changes outside "In scope".

## Constraints
- Keep the repo lean; add paragraphs, not a framework.
- Preserve the thesis: one copied `.claude/` folder plus root notes, no extra machinery until something hurts.
- Do not add the PreToolUse hook in this change.
- Do not imply `.claude/VERSION` is a full migration system; it is an identity stamp plus human update path.

## Progress (checkpoint here before any context reset)
- Done so far: context check passed (no git; markers in sync; plan assumptions held). Steps 1–5
  implemented; verification pass (step 6) complete — all referenced paths resolve, `.claude/VERSION`
  documented everywhere it's referenced, marker format untouched/in sync, no native-command overclaims.
- **Follow-up round complete** (see `plans/correctness-review-and-version-semantics.md`):
  - Correctness lens run over the full `.claude/` doc set — **clean**. The step-6 `(confirm)` grep
    matches every authored form (verified empirically); §Draft-vs-verified and §Tracked-vs-gitignored
    claims match `/init-cadence` + `/context-health` behavior; marker labels byte-identical.
  - Defined `.claude/VERSION` bump semantics (README §Updating step 1 + a comment in `.claude/VERSION`):
    bump on any change to the `.claude/` payload; project notes aren't part of the stamp.
  - Gitignore prominence + `(confirm)` authoring guidance: judged already clear — confirmed, not edited.
  - Re-ran cross-reference pass after edits: all paths resolve, markers still in sync, no overclaims.
- Next step: optional security/tests lenses if desired; otherwise this effort is complete.
- Files modified: `.claude/VERSION` (new + bump-rule comment), `README.md`, `AGENTS.md`,
  `.claude/templates/root-AGENTS.md`, `.claude/skills/init-cadence/SKILL.md`, `.claude/docs/context.md`,
  `.claude/commands/init-cadence.md`, `plans/cadence-version-codex-tiny-repos.md` (this checkpoint),
  `plans/correctness-review-and-version-semantics.md` (new — follow-up findings).

## Review lenses (run after implementation, each in a fresh session)
- [ ] Correctness: claims match actual copied files and command behavior.
- [ ] Security: no unsafe update instructions, no hidden overwrite/clobber path.
- [ ] Tests & edge cases: tiny repo, existing repo with old `AGENTS.md`, repo without git history, non-Claude agent reading only `AGENTS.md`.
