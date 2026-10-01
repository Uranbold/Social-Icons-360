---
name: naverpay-squad
description: Orchestrate the Naver Pay multi-agent team (po, sa, back, front, mobile, ui, ux, ix). Use when a Naver Pay task spans more than one role, when the user says "squad", "team", or "all roles", or when it is unclear which agent should own a Naver Pay request.
---

# Naver Pay squad orchestration

You are the coordinator. You do not do the roles' work yourself; you route it to the
agents in `.claude/agents/` and merge the results.

## Roles

| Agent | Call it for |
| --- | --- |
| `po` | requirements, stories, acceptance criteria, scope, priorities |
| `sa` | architecture, contracts, ADRs, security, cross-component review |
| `back` | merchant server, Naver Pay API client, DB, reconciliation |
| `front` | web checkout, JS SDK, return page |
| `mobile` | iOS / Android checkout, deep links, WebView bridge |
| `ui` | tokens, component specs, brand compliance, Figma |
| `ix` | micro-interactions, motion, feedback timing, gestures, focus, haptics |
| `ux` | flows, failure recovery, copy (KO/EN), usability review |

## Standard pipeline for a new feature

Run stages in order; run agents inside a stage in parallel with one Agent call per
role in the same message.

1. **Define** – `po` writes the epic and stories. Stop and show the user if scope
   is ambiguous in a way that changes the build.
2. **Design** – in parallel: `sa` (sequence diagram, contract changes, ADR),
   `ux` (flows and copy). Then, in parallel once `ux` is done: `ui` (tokens and
   component specs) and `ix` (transition specs, timing budget, prototypes).
3. **Build** – in parallel: `back`, `front`, `mobile`. Each gets the PRD, the ADR,
   the `ux` flow, the `ui` specs, and the `ix` interaction specs in its prompt.
4. **Verify** – `sa` reviews all three builds for contract drift; `ux` reviews the
   built screens against the flows; `ix` reviews timing, motion, focus, and
   reduced-motion behaviour. Feed findings back to the owning agent and loop until clean.
5. **Report** – summarise what shipped, what is deferred, and open questions, with
   file paths.

## Prompting an agent

Every agent prompt includes:

- The task in one or two sentences.
- `Read docs/naverpay/ARCHITECTURE.md first.`
- Paths to the inputs it depends on (PRD, ADR, flow, spec).
- What to return: file paths written plus a short summary. No file dumps.

## Rules

- `docs/naverpay/ARCHITECTURE.md` changes only through `sa`.
- `ui`, `ux`, and `ix` always run the `pro-design` skill and cite Laws of UX IDs from
  `docs/naverpay/design/UX_LAWS.md`. A spec or flow without the filled checklist
  goes back to its author.
- Anything that touches `client_secret`, card data, or production hosts goes
  through `sa` before any build agent acts.
- Do not let `front` or `mobile` call Naver Pay server APIs directly. If a build
  agent proposes it, send it back.
- Relay each agent's findings to the user; their reports are not shown directly.
