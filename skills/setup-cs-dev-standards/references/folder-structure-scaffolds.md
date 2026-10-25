# Folder Structure — Reference Scaffolds

Reference trees applying the rules in `folder-structure.md`. Not prescriptive — the shape bends to the stack, the rules do not. Read only the section matching the current repo's stack.

## Next.js full-stack (App Router)

```
src/
├── app/                          # routing layer only — thin
│   ├── (auth)/
│   │   ├── login/page.tsx        # imports from features/auth
│   │   └── register/page.tsx
│   ├── (dashboard)/
│   │   └── billing/page.tsx      # imports from features/billing
│   ├── api/
│   │   └── webhooks/
│   │       └── stripe/route.ts   # imports from features/billing
│   └── layout.tsx
│
├── features/
│   ├── auth/
│   │   ├── components/           # LoginForm.tsx, RegisterForm.tsx
│   │   ├── actions/              # loginAction.ts, logoutAction.ts
│   │   ├── data/                 # getSession.ts, createUser.ts
│   │   ├── types/                # authTypes.ts
│   │   └── index.ts              # barrel — the public surface
│   │
│   └── billing/
│       ├── invoices/             # sub-feature split
│       │   ├── components/
│       │   ├── actions/
│       │   └── data/
│       ├── subscriptions/
│       │   ├── components/
│       │   ├── actions/
│       │   └── data/
│       └── index.ts
│
└── shared/
    ├── components/               # Button.tsx, Input.tsx — used by 2+ features
    ├── lib/                      # db.ts, stripe.ts, logger.ts
    ├── hooks/                    # useDebounce.ts, useMediaQuery.ts
    └── types/                    # shared type definitions
```

Key moves: `app/` is a thin routing layer that imports from `features/`; business logic never lives in a `page.tsx` or `route.ts`. Sub-features (`invoices/`, `subscriptions/`) are the growth path when a feature gets too big, not more layers.

## Next.js + Express (monorepo)

```
apps/
├── web/                          # Next.js frontend
│   └── src/
│       ├── app/                  # routing — pages call API via shared client
│       ├── features/
│       │   ├── auth/
│       │   │   ├── components/
│       │   │   ├── hooks/
│       │   │   └── index.ts
│       │   └── billing/
│       └── shared/
│
├── api/                          # Express backend
│   └── src/
│       ├── routes/               # thin — maps URLs to feature handlers
│       │   ├── auth.routes.ts
│       │   └── billing.routes.ts
│       ├── features/
│       │   ├── auth/
│       │   │   ├── handlers/     # loginHandler.ts, registerHandler.ts
│       │   │   ├── services/     # authService.ts
│       │   │   ├── data/         # userRepository.ts
│       │   │   └── index.ts
│       │   └── billing/
│       └── shared/
│           ├── middleware/
│           ├── lib/              # db.ts, redis.ts
│           └── types/
│
└── packages/
    └── types/                    # types shared between web and api
        └── src/
            ├── auth.ts
            └── billing.ts
```

Key moves: both apps mirror the same `features/` + `shared/` layout — the rules don't change across the stack boundary. `packages/types/` is the only cross-app shared code; everything else is duplicated per app on purpose, because frontend and backend concerns rarely stay identical.

## Other stacks

Apply the same shape to whatever the stack calls its layers:

- **Vite SPA**: no routing folder — the router config imports from `features/`.
- **Remix / React Router 7**: `routes/` replaces `app/`, same thin-routing principle.
- **Python API (FastAPI, Django)**: `features/<name>/{routes,services,models,schemas}.py` — the layer names change, one-layer-per-folder holds.
- **Go service**: `internal/features/<name>/` with `handler.go`, `service.go`, `repository.go` as one file per layer when the feature is small; split into folders when it grows.

The decision in every case: name the features, give each one its own folder, keep one layer per folder, promote to `shared/` only on the second use.
