# Git Branching & PR Guidelines

## Scope

Applies to every write operation on the repository: branching, committing, pushing, pull requests, merging, rebasing, and conflict resolution. Does not apply to read-only inspection (`git log`, `git status`, `git diff`) unless followed by a write.

## Precedence

If the repository root contains any of the following, they override this file where they conflict. Read them before acting:

- `CONTRIBUTING.md`
- `.github/CODEOWNERS`
- `.github/pull_request_template.md`
- Branch protection rules (`gh api repos/:owner/:repo/branches/<default>/protection`)
- A commit template at `git config commit.template`

State the conflict in the next reply so the human sees which rule won.

## Branching

Trunk-based. One default branch, always deployable. Short-lived feature branches off it. No `develop`, no release branches.

- Branch from the default branch after `git fetch origin && git pull --rebase origin <default>`.
- Name: `<type>/<short-description>`. Lowercase, hyphen-separated, under 40 chars.
- Types: `feat`, `fix`, `chore`, `refactor`, `docs`, `test`.
- Examples: `feat/add-login`, `fix/cart-redirect`, `chore/upgrade-deps`.

## Commits

- One commit = one coherent change. Do not bundle unrelated edits.
- Title: imperative mood, under 72 chars. Body optional, wrapped at 72.
- Good: `Add rate limiting to login endpoint`
- Bad: `fixed stuff`, `WIP`, `updates`, `.`
- Do not amend or force-push commits already on a shared branch without explicit human confirmation.

## Branch size

- Target: under 300 lines of diff per PR.
- Hard stop at 500 lines: stop and ask the human whether to split before opening the PR.
- Branch lifetime target: under 48 hours of active work. Older → rebase onto the default branch before pushing.

## Pull requests

Before opening a PR, run locally and fix any failures:

1. Lint
2. Type-check (if applicable)
3. Tests
4. Build

PR title: imperative, under 72 chars.

PR description (use the repo's template if one exists; otherwise this structure):

- **What**: one-sentence summary.
- **Why**: the reason; linked issue if any.
- **How to test**: specific steps a reviewer can run.
- **Screenshots**: for any UI change.
- **Risk**: what could break; areas not touched that might still be affected.

Open as a draft while work is incomplete. Mark ready for review only when all checks pass.

## Merging

- **Squash-merge only.** Never merge-commit. Never rebase-merge into the default branch.
- Delete the branch after merge (local and remote).
- Required before merging:
  - Approvals per CODEOWNERS or branch protection; otherwise at least one non-author human approval.
  - All CI checks green.
  - No unresolved review comments.

## Hard stops (ask the human, do not proceed)

Stop and ask before:

- Pushing directly to the default branch.
- Force-pushing to any shared branch.
- `git reset --hard` on a branch with unpushed commits.
- Deleting an unmerged branch.
- Merging a PR you (the agent) opened — a human must approve.
- `git rebase` on a branch another contributor has committed to.
- Any git command with `--force`, `-f`, `--no-verify`.
- Any `gh pr merge --admin` or equivalent that bypasses protection rules.
- Any operation that rewrites shared history.

## When things go wrong

- **Merge conflict**: stop. List the conflicting files, show the conflicting hunks, ask the human how to resolve. Do not guess.
- **CI failure**: read the output, attempt one targeted fix, re-run. If it fails again, stop and report the error output.
- **Accidental commit to the default branch locally**: do not push. Create a branch at the current commit, reset the default branch to `origin/<default>`, open a PR from the new branch.
- **Pushed a secret or credential**: stop immediately. Report to the human. Do not attempt history rewrites on your own.

## Not covered

- Release tagging and versioning.
- Deployment pipelines beyond "CI must pass".
- Monorepo tooling (workspaces, nx, turborepo).
- Hotfix procedures for production incidents.
- Submodules.

For any of these, ask the human rather than inventing a workflow.

## Repo-level enforcement

The rules above are advisory text; mechanical enforcement lives in the repo configuration (branch protection, pre-commit hooks, required CI checks). That is a one-time human setup task, not something the agent executes on each git operation. See `branching-and-pr-repo-setup.md` in this references folder for the setup checklist.
