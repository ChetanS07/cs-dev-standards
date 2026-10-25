# Project Folder Structure

## The core principle

**Feature-first beats type-first.** Group files by the business capability they serve (auth, billing, dashboard), not by what kind of file they are (components, hooks, utils). Type-first layouts invite dumping: every new file has an obvious global bucket, so unrelated code piles up next to unrelated code. Feature-first forces the question *"what does this file belong to?"* before *"what kind of file is it?"* — which is the question that keeps a folder coherent.

## Placement

- **Features own their files.** A file used by one feature lives inside that feature's folder: `features/<name>/`.
- **Shared only after the second use.** A file is promoted to `shared/<layer>/` only when a second feature needs it. First use stays inside the originating feature; promoting earlier creates a `shared/` graveyard of things one feature uses.
- **No cross-feature internals.** A feature imports from another feature's root (its barrel), never from its internals. If something inside one feature is needed by another, that is the signal to promote it to `shared/`.

## One layer per folder

A folder holds one kind of thing: components, OR route handlers, OR data access, OR types — not a mix. This is the single rule that fixes the "everything in one folder" failure mode.

When a feature grows past what one folder cleanly holds, split by sub-feature (`features/billing/invoices/`, `features/billing/subscriptions/`), not by cramming more layers side by side. Volume is not the trigger; layer-mixing is.

The layers to recognise: UI component, route handler, server action, data access, type, test, config.

## Naming

- **Folders:** kebab-case (`user-profile/`).
- **React components:** PascalCase files (`ProfileCard.tsx`).
- **Everything else:** camelCase (`getUser.ts`, `authConfig.ts`).
- **Files are named for what they do.** `formatCurrency.ts`, `authMiddleware.ts`, `getActiveUser.ts`. A file named for its folder-role (`utils.ts`, `helpers.ts`, `common.ts`, `misc.ts`, `index2.ts`) is the signal that its purpose is not yet understood — name the function, not the bucket.

Naming conventions bend to match an existing codebase. The rule that holds is **consistency within a repo**: once a convention is set, it is locked for that repo.

## Barrels

One barrel per feature: `features/<name>/index.ts` is the only re-export file for that feature. No barrels inside features, none in `shared/`.

The alternative — no barrels, explicit paths everywhere (`features/auth/components/LoginForm`) — is a legitimate choice for teams that hit tree-shaking or circular-import pain. Pick one approach per repo and hold it; mixing creates the worst of both.

## Tests

Co-located by default: `foo.ts` sits next to `foo.test.ts`. Agents find and update co-located tests more reliably than mirrored trees, and the test moves with the file during refactors.

A mirrored `__tests__/` tree is the alternative, usually driven by monorepo tooling. Legitimate when the tooling requires it; the rule that holds is **one approach per repo**.

## What holds and what bends

**Holds across every repo** — loosening these makes the structure decorative:

- Feature-first over type-first.
- One layer per folder.
- Shared only after the second feature uses it.
- Named files, not bucket files (no `utils.ts`, `helpers.ts`, `common.ts`, `misc.ts`).
- Barrel at feature root is the only re-export surface (or no barrels at all — pick one).

**Bends to match the repo** — once chosen, locked:

- Case convention for folders and non-component files.
- Barrels vs explicit paths.
- Co-located tests vs mirrored test tree.
- Component file case (PascalCase is near-universal, but repo-wide kebab-case is valid).

## Red flags

Signals that a file is about to land in the wrong place:

- A second layer is about to join a folder (a route handler landing next to a component, or a DB query landing next to a route).
- The file's name is reaching for a bucket (`utils.ts`, `helpers.ts`, `index2.ts`) because its purpose has no one-word answer.
- The feature cannot be named in one word — the file probably belongs in `shared/`, or the feature itself has not been carved out yet.
- An import is reaching into another feature's internals rather than its barrel.

## Done when

Every file written sits under either `features/<name>/` or `shared/<layer>/`, matches the repo's chosen naming conventions, and no folder mixes layers.

## Reference scaffolds

Example trees applying the rules above for Next.js App Router, Next.js + Express monorepo, and notes for Vite / Remix / FastAPI / Go: see `folder-structure-scaffolds.md` in this references folder. Read it when scaffolding a new project or when the current repo's shape is unclear; skip it when working inside an established tree.
