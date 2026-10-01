# Motion and feedback tokens — Naver Pay checkout

Owned by the `ix` agent. Mirrored into `docs/naverpay/ui/tokens.json` under
`motion.*` by `ui`. Change here first.

## Durations

| Token | ms | Use |
| --- | --- | --- |
| `motion.duration.instant` | 100 | tap acknowledgement, pressed state |
| `motion.duration.fast` | 200 | hover, focus ring, small reveals |
| `motion.duration.base` | 300 | sheet open/close, page-level state change |
| `motion.duration.slow` | 500 | success confirmation, result reveal |

## Easings

| Token | Curve | Use |
| --- | --- | --- |
| `motion.easing.standard` | cubic-bezier(0.2, 0, 0, 1) | most transitions |
| `motion.easing.decelerate` | cubic-bezier(0, 0, 0, 1) | entering elements |
| `motion.easing.accelerate` | cubic-bezier(0.3, 0, 1, 1) | leaving elements |

## Feedback timing budget (every action)

| At | Buyer sees |
| --- | --- |
| 0–100 ms | pressed state on the control |
| ≤ 400 ms | progress indicator or next screen (L05) |
| 3 s | message: "네이버페이 응답을 기다리는 중입니다" / "Waiting for Naver Pay" |
| 10 s | message plus a safe exit: "주문 상태 확인" / "Check order status" (L20) |
| timeout (backend value) | failure state with recovery action, never a blank screen |

## Reduced motion

Every animated transition has a reduced-motion variant: opacity-only or an
instant swap, same duration token, meaning preserved. Implemented with
`@media (prefers-reduced-motion: reduce)` on web and the platform accessibility
flag on iOS and Android.

## Haptics

| Outcome | iOS | Android |
| --- | --- | --- |
| Pay button pressed | `.light` impact | `CLOCK_TICK` |
| Payment success | `.success` notification | `CONFIRM` |
| Payment failed | `.error` notification | `REJECT` |

Fire once per outcome. Never on re-render, never during the Naver Pay window.
