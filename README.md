# context-loop

> **Quick start:** Copy `.claude/` → run `/init-cadence` → `/plan <your change>`.
> *(Slash commands are Claude Code; in Codex, Cursor, or other tools, read `AGENTS.md` and run the
> same loop by hand — [details below](#how-the-notes-work-the-short-version).)*

A drop-in folder that teaches any coding AI to work on your project the right way: **know your
project, plan before it codes, change one thing at a time, and check its own work.** Copy one
folder, run one command, and you're set.

It does two jobs:
- **Set up** a repo — new or one that already has code — so the AI starts every session already
  knowing what it's working on.
- **Improve** a repo whose AI notes are thin or out of date — bring it up to this standard.

---

## Two ways to install

- **Copy into the repo** (`.claude/`) — the default. The kit travels with the code; every
  collaborator gets it. Recommended for teams.
- **Install globally** (`~/.claude/`) — solo convenience. Copy the kit's `.claude/` *contents*
  into `~/.claude/` once; commands and skills are then available in any repo you open, with no
  `.claude/` folder required in the repo itself.

The three steps below describe the copied (default) path. For global install, copy `<kit>/.claude/`
*contents* into `~/.claude/` and run `/init-cadence` in any repo — the engine files (`templates/`,
`guide/`, `checklists/`, `skills/`) resolve from `~/.claude/` automatically when no repo `.claude/`
is present.

---

## Set up your repo — 3 steps

> **First, get the kit:** `git clone` (or download and unzip) this repo — `<kit>` in the commands below is that folder.

1. **Copy the `.claude/` folder** into the top of your project. That's the whole kit — *one
   folder*, nothing else to chase down.
   > **Already have a `.claude/`?** Any repo you've used Claude Code in does — it holds your
   > `settings.local.json` and custom commands. **Merge, don't replace.** Never drag it in with
   > Finder and choose *Replace* — that **deletes** your existing `.claude/`. Copy the *contents* in:
   > `cp -R <kit>/.claude/. <your-repo>/.claude/` (note the trailing `/.`), or `rsync -a
   > <kit>/.claude/ <your-repo>/.claude/`. That adds the kit beside your settings, untouched.
   > (Caveat: the copy overwrites *same-named* files — the kit ships a `.claude/.gitignore` (it only
   > excludes `.DS_Store`) plus the commands `plan`/`review`/`context-health`/`init-cadence`/`cadence-audit`.
   > If you already keep your own `.claude/.gitignore` or a command with one of those names, merge those by hand.)
2. **Run `/init-cadence`.** It reads your project and writes two notes the AI will read from then on:
   - **`AGENTS.md`** — the main note *any* AI reads (Claude, Codex, Cursor…): what the project
     is, how to build and test it, and the rules it must never break.
   - **`CLAUDE.md`** — a short cover note just for Claude that points to `AGENTS.md`.

   It also writes a note for each big folder — verifying names, routes, and paths against your real
   code, and asking you about anything it can't — and makes an empty `plans/` folder.
3. **That's it.** The AI now reads those notes automatically, every session.

> **`/init-cadence` replaces Claude Code's built-in `/init`** — you don't need both. (Already ran
> `/init`? No harm — `/init-cadence` keeps your existing `CLAUDE.md` and just adds to it.)

> **New empty repo?** It sets up the skeleton and grows with you.
> **Repo that already has code?** This is where it shines — it reads your real code and fills the
> notes in for you. The more code there is, the more it captures.

> **Slash commands not showing up?** Claude Code loads `.claude/` from the folder you **open as
> your workspace** (and its parents) — it doesn't reach *down* into nested subfolders at startup.
> So if your repo sits inside a parent folder and you open the **parent**, the kit goes unseen.
> Fix: open the repo that holds `.claude/` as your workspace — not a folder that contains it.
> (Or move `.claude/` up to the folder you actually open.)

## Already have a repo — even one with its own AI notes?

**Don't delete anything.** Copy `.claude/` in (**merging**, not replacing — see the callout in step 1) and run `/init-cadence`:
- No notes yet → it writes them from your code.
- You already have an `AGENTS.md` / `CLAUDE.md` → it shows what it would change and **asks
  first**. It *improves* your notes; it never quietly overwrites them.

Same one folder, same one command — that's the "bring an old repo up to standard" path.

---

## Then do actual work — the loop

Setup happens **once**. After that, whenever you want to build or fix something, you start it
with **`/plan`** and a few words about the thing:

- `/plan add a dark-mode button`
- `/plan fix the login bug on Safari`

(`<your change>` just means "the thing you're about to do.") Here's the rhythm `/plan` kicks off:

1. **Plan before code.** The AI writes a short plan and checks it against your *real* code before
   typing a single line. You approve or fix it.
2. **Small steps, reset early.** One change at a time. Clear the chat early — around 30% full (a 40%
   auto-compaction backstop catches you if you blow past it) — so the AI stays sharp; the plan file
   remembers where you were, so nothing is lost.
3. **Fresh-eyes review.** Check the work in a *clean* chat, one lens at a time: `/review
   correctness`, then `/review security`, then `/review tests`. (A tired chat reviewing its own
   work just agrees with itself.)

Anytime, run **`/context-health`** to see if the AI's notes have fallen behind your code — or
**`/cadence-audit`** for a deep, multi-agent pass that catches what the structural check can't (it
needs a harness with multi-agent orchestration, so it's the heaviest rung).

> **Using Codex, Cursor, or another tool?** `/plan`, `/review`, and `/context-health` are Claude
> Code commands. Everywhere else, `AGENTS.md` spells out the same loop in plain prose under *"How to
> work here"* — follow it by hand. The discipline is portable; the slash commands are the Claude-only
> convenience layer.

> **Not using AI at all?** The cadence is a plain engineering discipline first — plan, one small diff,
> fresh-eyes review, notes that track the code. `AGENTS.md` is written for a human reader too, and the
> plan template and review checklist are fill-in-by-hand. You write the notes yourself instead of
> running `/init-cadence`; [`.claude/guide/context.md`](.claude/guide/context.md) has the by-hand
> setup steps. You lose the automation, not the method.

---

## How the notes work (the short version)

- **`AGENTS.md` is the source of truth** — plain and tool-agnostic, so *any* agent reads it.
  Claude pulls it in through a one-line `@AGENTS.md` inside `CLAUDE.md`.
- **Big folders can get their own `CLAUDE.md`** — auto-loaded only while the AI works in that
  folder, so it learns one part of the codebase without re-reading the whole thing.
- **Notes carry a freshness stamp** (`Last verified:`), and `/context-health` flags the ones that
  fell behind. The rule that keeps them honest: update a note in the *same change* that changes
  the code. Full guide: [`.claude/guide/context.md`](.claude/guide/context.md).
- **Portable vs Claude-only.** `AGENTS.md` — and the loop it describes — is the portable baseline
  *any* agent reads. The slash commands, the skills, and the auto-loaded nested `CLAUDE.md` files
  are Claude Code extras layered on top; other tools read `AGENTS.md` and run the same loop by hand.

> **Tiny repo?** A 3-file script doesn't need the full hierarchy. Root `AGENTS.md` + root
> `CLAUDE.md` can be the *whole* cadence — skip per-module notes and heavy planning until the repo
> grows. Keep the basic loop, though: plan, one small diff, verify. Add machinery only when the
> repo gets big enough that going without it starts to hurt.

---

## Updating the cadence in an existing repo

The kit improves over time. To pull a newer version into a repo that already has it:

1. **Check the stamp.** Compare the repo's `.claude/VERSION` against this master repo's — that's
   how you tell whether there's anything newer to pull. The master bumps the stamp on *any* change
   to the `.claude/` payload (skills, commands, templates, guide, checklists), so matching stamps
   mean identical tooling and a difference means there's something to re-copy. (In global install
   mode, the stamp lives at `~/.claude/VERSION` and governs all repos that use the global kit.)
2. **Re-copy the kit — merging, not replacing.** Use the same safe copy as first install
   (`cp -R <kit>/.claude/. <your-repo>/.claude/` — note the trailing `/.` — or `rsync -a
   <kit>/.claude/ <your-repo>/.claude/`) — **never** Finder-*Replace* the folder, which would delete
   your `settings.local.json` and custom commands. As in step 1, the copy overwrites *same-named*
   files, so a `.claude/.gitignore` or command you customized gets replaced — re-merge it. That
   refreshes the tooling (skills, commands, templates, guide, checklists) and the `VERSION` stamp.
   One gap a copy can't close: if a newer version *removed* a kit file, the copy won't prune the
   orphan — delete it by hand.
3. **Leave your front-door notes alone.** Your repo's root `AGENTS.md` and `CLAUDE.md` are
   project-specific; the copy doesn't touch them, and you shouldn't either.
4. **Re-run `/init-cadence` only if your project context needs a refresh** — new modules to
   document, or thin notes to merge. It asks before changing existing notes; it never clobbers them.
5. **Run `/context-health`** afterward to confirm the refreshed tooling still lines up with your notes.

Re-copying `.claude/` refreshes the cadence *tooling and templates* — it does **not** re-verify
your project notes. Those stay only as accurate as your last `/init-cadence` or `/context-health`
pass. This is an identity stamp plus a human update path, not an automatic migration.

---

## What's in the folder

```
.claude/                              # ← THE drop-in cadence — copy this one folder into any repo
  VERSION                             # cadence stamp — which version of the kit this repo copied
  .gitignore                          # keeps macOS .DS_Store cruft out of the copied folder
  skills/context-loop/SKILL.md        # the loop, as a skill (auto-loads)
  skills/init-cadence/SKILL.md        # /init-cadence — bootstraps a repo (run once after copying)
  commands/init-cadence.md            # /init-cadence — set up (or improve) a repo
  commands/plan.md                    # /plan — start a change
  commands/review.md                  # /review — single-lens review pass
  commands/context-health.md          # /context-health — audit context for staleness (structural)
  commands/cadence-audit.md           # /cadence-audit — deep multi-agent audit (the higher rungs)
  templates/root-AGENTS.md            # master AGENTS.md — the portable baseline (init fills it in)
  templates/root-CLAUDE.md            # master CLAUDE.md shim — @AGENTS.md + Claude-only bits
  templates/module-claude-md.md       # per-directory CLAUDE.md — auto-loaded module context
  templates/plan.md                   # fill-in plan template (the source of truth)
  checklists/review.md                # one-lens-at-a-time review passes
  guide/context.md                    # the context-file guide (what / why / when / how)
  workflows/cadence-audit.js          # the /cadence-audit multi-agent review workflow

AGENTS.md   CLAUDE.md   README.md     # this repo's own context + readme (init generates the first two for your repo)
```

### What each file is for (plain language)

Picture your codebase as a big house the agent works on. Everything that *runs* the cadence
lives in one folder — **`.claude/`** — and that's the only folder you copy into a new project.
Two notes get *written* at the front door for your specific repo.

**The two front-door notes (`/init-cadence` writes these for you)**
- [AGENTS.md](AGENTS.md) — The note on the front door, written so *any* assistant can read it
  (Claude, Codex, Cursor…): what the project is, the commands, the rules it must never break.
  The source of truth.
- [CLAUDE.md](CLAUDE.md) — A short cover sheet just for Claude. It says "read AGENTS.md first"
  (`@AGENTS.md`) and adds a few Claude-only notes — how to reset safely, where the shortcuts live.

**Inside `.claude/` — the drop-in cadence you copy in**
- [.claude/VERSION](.claude/VERSION) — A one-line stamp of which version of the kit this repo
  copied (the line reads `cadence: <x.y.z>`). It's an identity marker, not a migration system: compare it
  against the master repo to see whether there's a newer cadence to pull in.
- [.claude/skills/init-cadence/SKILL.md](.claude/skills/init-cadence/SKILL.md) — Run
  `/init-cadence` *once*, right after copying the folder in. It reads your repo and writes the
  two front-door notes above, so you don't start from a blank page.
- [.claude/skills/context-loop/SKILL.md](.claude/skills/context-loop/SKILL.md) — The agent's
  rulebook, loaded automatically: plan first, make small changes, let a *fresh* helper check the work.
- [.claude/templates/](.claude/templates/) — The blank fill-in notes the skills copy from:
  `root-AGENTS.md` and `root-CLAUDE.md` (the two front-door notes), `module-claude-md.md` (a note
  for *one room* of a bigger house, with a "last checked on ___" date stamp), and `plan.md` — the
  sketch you make before building, like planning a Lego build before opening the box.
- [.claude/checklists/review.md](.claude/checklists/review.md) — A checklist for double-checking
  one question at a time: works? safe? tested? — like proofreading once for spelling, once for grammar.
- [.claude/guide/context.md](.claude/guide/context.md) — The full guidebook for *why/how* to write
  those notes and keep them fresh. Also the measuring stick when you bring another project's docs
  here to improve them.

**The buttons you press**
- `/init-cadence` — set up (or improve) a repo. Run once.
- `/plan <change>` — start a change (writes the plan, checks it against your code).
- `/review correctness` · `/review security` · `/review tests` — a fresh helper checks the work, one lens at a time.
- `/context-health` — scan all the notes and flag the ones that fell behind the code (structural).
- `/cadence-audit` — deep multi-agent review of the whole setup (the higher rungs context-health can't see); report-only, heavy.

---

## Why it works

- A plan checked against the real codebase catches wrong assumptions before they become wrong code.
- Small diffs stay reviewable and keep the AI's context lean.
- Resetting early keeps every session in its high-quality range; the plan file carries the thread across resets.
- One lens per review pass catches what a blended, self-reviewing pass misses.
- Fresh, accurate notes mean a clean session is never a blank one — the AI knows your project before it starts.

---

MIT — use it, fork it, ship it.
