# Git Workflow — Repo Setup Checklist

One-time human setup to make `branching-and-pr.md` mechanically enforced. Agents do not run this on each task; use when first configuring a repo.

## GitHub branch protection on the default branch

Set via **Settings → Branches → Add rule** or `gh api`:

- Require a pull request before merging.
- Require 1 approval from a non-author.
- Dismiss stale approvals on new commits.
- Require status checks to pass: lint, type-check, tests, build.
- Require branches to be up to date before merging.
- Require conversation resolution before merging.
- Restrict who can push to matching branches (empty list = nobody direct-pushes).
- Allow squash merging only. Disable merge commits and rebase merging in **Settings → General → Pull Requests**.
- Automatically delete head branches after merge.
- Disallow force pushes.
- Disallow branch deletions.

## Repository files

- `.github/pull_request_template.md` — matches the PR description structure in `branching-and-pr.md`.
- `.github/CODEOWNERS` — assigns required reviewers by path.
- `CONTRIBUTING.md` — points to the main workflow file: `See branching-and-pr.md for branching, commits, and PR rules.`
- `.gitignore` — excludes secrets, env files, build artifacts.

## Local pre-commit hooks

Install via Husky, pre-commit, or `.git/hooks/`. At minimum:

- `pre-commit`: run lint and type-check on staged files.
- `commit-msg`: reject titles over 72 chars or starting with `wip`, `.`, or empty.
- `pre-push`: block pushes to the default branch; run tests.

## CI pipeline (required status checks)

Every PR must run and pass:

- Lint
- Type-check
- Unit tests
- Build
- Secret scan (e.g. `gitleaks`, `trufflehog`)

Mark these as required in branch protection so GitHub blocks the merge button until they are green.
