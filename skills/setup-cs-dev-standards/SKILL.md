---
name: setup-cs-dev-standards
description: Copy this plugin's standards reference files into the current repo and register them in the repo's CLAUDE.md. Run once per repo. Writes branching, folder-structure, coding-standards, and (optional) design-system reference files into `docs/standards/` within the repo, then appends a Standards section to CLAUDE.md pointing to those repo-relative paths with per-reference "read before:" triggers. Also prompts for design system URLs or Claude Design names and, only if provided, writes a design-system reference. Use when the user says "set up standards", "/setup-cs-dev-standards", "install the cs-dev-standards plugin", "add coding standards to this repo", or is starting a new repo and wants the standards wired up.
---

# setup-cs-dev-standards

A **user-invoked** scaffolding skill. Run it once per repo to copy this plugin's reference files into the repo and register them in the repo's `CLAUDE.md`. After this skill runs, the standards live inside the repo itself — they travel with it on clone, and the repo owns its own copies.

## What this skill does

1. Confirm there is a target repo (a `.git` directory or clear project root at the current working directory).
2. Copy the plugin's reference files from `<plugin>/skills/setup-cs-dev-standards/references/` into `<repo>/docs/standards/` — creating the destination folder if missing, and asking per-file before overwriting any existing file that differs from the plugin's version.
3. Prompt the user for design system inputs (URLs or Claude Design names). Only if provided, write `<repo>/docs/standards/design-system.md`.
4. Append (or replace) a `## Standards` section in the repo's `CLAUDE.md` pointing to each copied file with a short "read before:" trigger.
5. Report what was copied, skipped, overwritten, or asked about.

## Reference files bundled with this plugin

Source paths, relative to this SKILL.md's directory:

- `references/branching-and-pr.md` — git branch naming, strategy, commits, PRs, merging, hard stops
- `references/branching-and-pr-repo-setup.md` — one-time human setup checklist (branch protection, hooks, CI). Not pointed at from CLAUDE.md directly; referenced from `branching-and-pr.md`.
- `references/folder-structure.md` — feature-first placement rules
- `references/folder-structure-scaffolds.md` — stack-specific example trees. Not pointed at from CLAUDE.md directly; referenced from `folder-structure.md`.
- `references/coding-standards.md` — strict mode, naming, errors, zod boundaries, agent-generation rules
- `references/design-system.md` — **created at runtime only if the user supplies inputs**; otherwise never written.

All of the above (except `design-system.md` when skipped) are copied as-is from the plugin into `<repo>/docs/standards/`, preserving filenames.

## Steps

### Step 1 — Confirm the target repo

The current working directory should contain a `.git` directory or a clear project root (`package.json`, `pyproject.toml`, `go.mod`, etc.). If unclear, ask the user to confirm the repo path. Do not run this skill outside a repo.

### Step 2 — Prepare the destination

Create `<repo>/docs/standards/` if it does not exist. Do not touch the folder's contents yet.

### Step 3 — Copy reference files

For each file in the plugin's `references/` folder (except `design-system.md`, which is handled in Step 4):

- **If the destination file does not exist** → copy it. Report "created".
- **If the destination file exists and is byte-identical to the plugin's version** → skip. Report "unchanged".
- **If the destination file exists and differs** → stop and ask the user: "`<filename>` in this repo differs from the plugin's current version. Overwrite, keep repo version, or show diff?" Honor the choice per file. Never silently overwrite — a diverged file may be a deliberate per-repo customization.

Do this for all five bundled files (`branching-and-pr.md`, `branching-and-pr-repo-setup.md`, `coding-standards.md`, `folder-structure.md`, `folder-structure-scaffolds.md`) in a single batch so the user can approve the overwrites together.

### Step 4 — Ask about the design system

Ask the user **exactly once**:

> Do you have a design system to register for this repo? Share either:
> - URLs (Figma, hosted docs, Claude Design artifact links like `claude.ai/code/artifact/...`), or
> - Claude Design names you've saved.
>
> If none, say "skip".

- If the user replies "skip" or gives no content: do **not** create `design-system.md` and do **not** include a design-system pointer in CLAUDE.md.
- If the user provides URLs or names: write `<repo>/docs/standards/design-system.md` in the format below. Do not fetch the URLs. Do not fabricate design tokens.

**design-system.md format:**

```markdown
# Design System References

User-supplied design system sources for this repo. Treat each entry as the source of truth for visual language, components, tokens, and interaction patterns.

## Sources

- <URL or Claude Design name 1>
- <URL or Claude Design name 2>
- ...

## How to use

Before implementing or modifying any UI, read the sources above and align colors, typography, spacing, and components to what they define. If a source is a Claude Design artifact, open it and extract tokens as needed.
```

### Step 5 — Append the Standards section to CLAUDE.md

Append (do not overwrite) the following block to `<repo>/CLAUDE.md`. If `CLAUDE.md` does not exist, create it with just this block. If a `## Standards` section already exists from a previous run, replace **only that section** rather than duplicating or appending.

**Block to write (omit the design-system bullet if Step 4 was skipped):**

```markdown
## Standards

This repo uses the `cs-dev-standards` plugin. Reference files live under `docs/standards/`. Read the matching file **before** doing the work it covers.

- **Git branching & PRs** → `docs/standards/branching-and-pr.md`
  Read before: creating a branch, naming a branch, writing a commit message, opening or updating a pull request.
- **Project folder structure** → `docs/standards/folder-structure.md`
  Read before: creating a new file or folder, deciding where new code should live, moving or renaming files, scaffolding a new module.
- **Coding standards** → `docs/standards/coding-standards.md`
  Read before: writing or editing any code — naming, formatting, patterns, error handling, imports, tests.
- **Design system** → `docs/standards/design-system.md`
  Read before: building or modifying any UI — colors, typography, spacing, components, interaction patterns.

If a reference file does not exist, skip its rule rather than inventing content.
```

Use paths exactly as shown — repo-relative, not absolute. The agent's working directory in future sessions will be the repo root, so `docs/standards/<file>` resolves without further qualification.

### Step 6 — Report

Tell the user, in short form:
- Which files were created, skipped (unchanged), or overwritten
- Whether the design-system pointer was included or skipped
- Where the files live (`<repo>/docs/standards/`) and that they can edit them directly — edits stay local to this repo and will not be overwritten unless they re-run this skill and approve an overwrite

Do not re-run the setup unless the user explicitly asks ("re-run setup-cs-dev-standards", "pull latest standards from the plugin").

## Guardrails

- **One section only.** The `## Standards` section is the only thing this skill writes to `CLAUDE.md`. Do not add preamble, do not reorder existing CLAUDE.md content, do not touch other sections.
- **Idempotent for identical files.** Running the skill twice with no changes in the plugin should produce no diffs and no prompts.
- **Never silently overwrite a diverged file.** A file that differs may be a deliberate per-repo customization; always ask.
- **No fabrication.** Do not invent branching strategies, folder layouts, or design tokens. The reference files are the source of truth.
- **Design system is opt-in.** If the user skips Step 4, `design-system.md` and its CLAUDE.md pointer must not exist.
- **Repo-relative paths only.** Never write absolute paths into CLAUDE.md — the plugin install location is not stable across machines or reinstalls.
