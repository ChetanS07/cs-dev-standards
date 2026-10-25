# cs-dev-standards

A Claude Code plugin that **copies** project-standards reference files into a repo and registers them in that repo's `CLAUDE.md`. Pairs with [mattpocock/skills](https://github.com/mattpocock/skills): mattpocock's skills own execution and workflow; this plugin is purely reference material.

## Model

Each repo gets its **own copy** of the reference files under `docs/standards/`. Standards travel with the repo on clone, and each repo can diverge from the plugin defaults without affecting any other repo. Trade-off: plugin updates do not propagate automatically — re-run the setup to pull new defaults.

## Install

Push this plugin to a GitHub repo, then in Claude Code:

```
/plugin marketplace add <your-github-user>/cs-dev-standards
/plugin install cs-dev-standards@cs-dev-standards-marketplace
```

Restart the session.

## Use

In any target repo, from the repo root, once:

```
/setup-cs-dev-standards
```

This will:
1. Create `docs/standards/` in the repo.
2. Copy the plugin's reference files into it (asking per-file before overwriting any diverged file).
3. Ask for design system URLs or Claude Design names; if provided, write `docs/standards/design-system.md`. If "skip", no design-system file is written.
4. Append a `## Standards` section to the repo's `CLAUDE.md` with repo-relative pointers and "read before:" triggers.

## What's inside

```
cs-dev-standards/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
└── skills/
    └── setup-cs-dev-standards/
        ├── SKILL.md
        └── references/
            ├── branching-and-pr.md
            ├── branching-and-pr-repo-setup.md
            ├── coding-standards.md
            ├── folder-structure.md
            └── folder-structure-scaffolds.md
```

`design-system.md` is not in the plugin — it is only ever created inside a repo, at setup time, if the user provides inputs.

## Updating the defaults

Edit any file under `skills/setup-cs-dev-standards/references/` in this plugin repo and push. Existing repos won't change until someone re-runs `/setup-cs-dev-standards` in them and approves the overwrites.

## Not included

- Execution flows (grilling, spec-making, implementation) — those live in mattpocock/skills.
- Automatic propagation of updates to installed repos.
