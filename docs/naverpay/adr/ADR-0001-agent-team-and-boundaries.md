# ADR-0001: Role-based agent team and component boundaries
Status: accepted
Date: 2026-10-01

## Context
A Naver Pay integration spans product, architecture, backend, web, mobile, and
design. Work done by one undifferentiated agent tends to leak secrets into clients,
skip failure states, and drift the API contract between web and mobile.

## Decision
Seven agents with fixed ownership (see ARCHITECTURE.md §5). The backend is the only
component that holds Naver Pay credentials and calls its server APIs. The Solution
Architect alone edits ARCHITECTURE.md and must record an ADR for each change.
The `/naverpay-squad` skill coordinates multi-role tasks in a define → design →
build → verify pipeline.

## Consequences
- Slightly more hand-offs per feature; far fewer contract mismatches.
- Front and mobile cannot ship without a `ui` spec and `ux` flow, which forces
  failure states to be designed before being coded.

## Alternatives considered
- One generalist agent: faster for tiny tasks, rejected for payments work.
- Backend + frontend only: lost mobile return-link handling and design review.
