# Project notes

## Naver Pay multi-agent setup

This repo carries a role-based agent team for building a merchant Naver Pay
integration. Roles live in `.claude/agents/`:

`po` · `sa` · `back` · `front` · `mobile` · `ui` · `ux`

- Shared source of truth: `docs/naverpay/ARCHITECTURE.md` (owned by `sa` only).
- Coordinator: `/naverpay-squad` skill routes multi-role work and runs the
  define → design → build → verify pipeline.
- Direct a single role explicitly, e.g. "use the `back` agent to add partial cancel".

Hard rules every agent follows:

1. Only the backend calls Naver Pay server APIs; `client_secret` never leaves it.
2. Approve and cancel are idempotent on `paymentId`.
3. Amount from the approve response is verified against the stored order before PAID.
4. No secrets or production hosts in the repo; sandbox and production switch by config.

## Existing content

`index.html`, `style.css`, `css/` are the original rotating social icons demo and
are unrelated to the Naver Pay work. Leave them untouched unless asked.
