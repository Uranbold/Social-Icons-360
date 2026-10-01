# Naver Pay Integration — Shared Architecture Reference

Every agent in `.claude/agents/` reads this file before working. It is the single
source of truth for names, flows, environments, and boundaries. Change it through the
Solution Architect (`sa`) agent only.

## 1. What we are building

A merchant-side Naver Pay checkout integration:

- Web checkout (desktop + mobile web) using the Naver Pay JS SDK
- Native mobile checkout (iOS / Android) via in-app WebView bridge or app-to-app
- Merchant backend that owns order state, calls Naver Pay server APIs, and handles
  approve / cancel / history reconciliation
- Admin / ops views for refunds, disputes, and settlement reconciliation

## 2. Naver Pay payment flow (canonical)

```
Buyer            Front / Mobile            Merchant Backend              Naver Pay
  |  tap "Naver Pay"   |                         |                           |
  |------------------->| POST /orders            |                           |
  |                    |------------------------>| create order (PENDING)    |
  |                    |<------------------------| orderId, merchantPayKey   |
  |                    | SDK .open({...})        |                           |
  |                    |---------------------------------------------------->|
  |  authenticate & pay in Naver Pay window                                  |
  |                    |<----------------------------------------------------|
  |                    | redirect returnUrl?resultCode=Success&paymentId=... |
  |                    |------------------------>| POST /payments/approve    |
  |                    |                         |-------------------------->| apply/payment
  |                    |                         |<--------------------------| detail (paymentId, amount, ...)
  |                    |                         | verify amount == order    |
  |                    |                         | order -> PAID             |
  |                    |<------------------------| receipt                   |
```

Hard rules:

1. **The backend is the only component that calls Naver Pay server APIs.** Front and
   mobile never see `client_secret`.
2. **Approve must be idempotent** on `paymentId`. Double approve = silent no-op with the
   stored result.
3. **Always verify** `totalPayAmount` from the approve response against the stored
   order before marking PAID.
4. **Order state machine**: `PENDING → APPROVING → PAID → (PARTIAL_CANCELLED | CANCELLED)`
   plus `FAILED` and `EXPIRED`. No other transitions.

## 3. Naver Pay API surface (merchant side)

| Purpose | Method / path | Owner |
| --- | --- | --- |
| Reserve (JS SDK) | `Naver.Pay.create({...}).open({...})` | front / mobile |
| Approve | `POST /{partnerId}/naverpay/payments/v2.2/apply/payment` | back |
| Cancel (full / partial) | `POST /{partnerId}/naverpay/payments/v1/cancel` | back |
| History / reconciliation | `POST /{partnerId}/naverpay/payments/v2.2/list/history` | back |

Headers on every server call:

```
X-Naver-Client-Id:      <client id>
X-Naver-Client-Secret:  <client secret>      # backend only, from secret manager
X-NaverPay-Chain-Id:    <chain id>
X-NaverPay-Idempotency-Key: <uuid>           # on approve and cancel
```

Hosts:

| Env | API host | SDK |
| --- | --- | --- |
| sandbox | `https://dev-pub.apis.naver.com` | `https://nsp.pay.naver.com/sdk/js/naverpay.min.js` with `mode: "development"` |
| production | `https://apis.naver.com` | same SDK with `mode: "production"` |

Verify exact paths, field names, and SDK option names against the current Naver Pay
developer docs before implementing. Field names below are the agreed internal contract
and must match what the backend actually sends.

## 4. Internal API contract (merchant backend ⇄ clients)

```
POST   /api/v1/orders                      -> { orderId, merchantPayKey, amount, items[] }
POST   /api/v1/payments/naverpay/approve   { orderId, paymentId }  -> { status, receipt }
POST   /api/v1/payments/naverpay/cancel    { orderId, amount?, reason } -> { status }
GET    /api/v1/orders/{orderId}            -> order + payment summary
POST   /webhooks/naverpay                  (if enabled) signature-verified
```

Error envelope: `{ code: "NP_xxx", message, retryable: bool, traceId }`.

## 5. Component ownership

| Area | Agent | Owns |
| --- | --- | --- |
| Requirements, acceptance criteria, priorities | `po` | `docs/naverpay/prd/`, backlog |
| Architecture, contracts, ADRs, security model | `sa` | this file, `docs/naverpay/adr/` |
| Merchant backend services, DB, Naver Pay client | `back` | `server/` (or project's backend dir) |
| Web checkout, SDK integration, return page | `front` | `web/` (or project's frontend dir) |
| iOS / Android checkout, WebView bridge, deep links | `mobile` | `mobile/` |
| Visual design system, components, tokens | `ui` | design tokens, component specs |
| Flows, usability, error-state copy, research | `ux` | `docs/naverpay/ux/` |
| Micro-interactions, motion, feedback timing, gestures, focus, haptics | `ix` | `docs/naverpay/ix/`, `docs/naverpay/design/MOTION.md` |

## 6. Non-functional requirements

- PCI scope: none of our systems touch card data; Naver Pay does. Keep it that way.
- Secrets: `client_secret` in a secret manager, never in repo, env files committed, or
  client bundles.
- Logging: never log full `paymentId` + buyer PII together; mask to last 4.
- Timeouts: approve call 10s with one retry on network error using the same
  idempotency key.
- Reconciliation: nightly history pull compares Naver Pay records with local orders;
  mismatches raise an ops alert.
- Accessibility: checkout button and result pages meet WCAG 2.1 AA.
- Locale: Korean primary, English secondary. Currency KRW, no decimals.

## 7. Definition of done (any ticket)

- [ ] Acceptance criteria from `po` satisfied
- [ ] Contract in §4 unchanged, or changed via an ADR from `sa`
- [ ] Tests: unit for logic, integration for Naver Pay client against sandbox
- [ ] No secrets in diff
- [ ] UX reviewed error / loading / empty states; UI matches tokens
- [ ] IX reviewed timing budget, motion, focus, reduced motion, haptics
- [ ] Mobile and web verified in sandbox end to end
