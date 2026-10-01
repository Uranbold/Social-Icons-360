# Laws of UX — applied to Naver Pay checkout

The `ui` and `ux` agents cite these by ID in every spec, flow, and review. A
recommendation without a law ID or a measured reason is an opinion, not a finding.

| ID | Law | What it means for checkout |
| --- | --- | --- |
| L01 | Fitts's Law | Pay button ≥ 48×48 dp, full width on mobile, pinned to the thumb zone. Destructive actions (cancel payment) small and away from it. |
| L02 | Hick's Law | One primary action per screen. Payment method list ≤ 5 visible options, Naver Pay first if it is the default. |
| L03 | Jakob's Law | Follow the checkout patterns buyers already know from Naver Shopping and major Korean malls. Do not invent a novel pay flow. |
| L04 | Miller's Law | Order summary groups into ≤ 7 chunks: items, discounts, shipping, total. Collapse item lists beyond 3. |
| L05 | Doherty Threshold | Feedback within 400 ms of every tap. Show a skeleton or spinner before the Naver Pay window opens and while approve runs. |
| L06 | Peak–End Rule | The result screen is the peak and the end. Make success unambiguous and failure recoverable with one clear next step. |
| L07 | Zeigarnik Effect | Show progress (cart → pay → done). If the buyer closes the Naver Pay window, keep the order and offer "resume payment". |
| L08 | Tesler's Law | Complexity (idempotency, amount checks, retries) lives in the backend. Never ask the buyer to "try again if you were charged". |
| L09 | Aesthetic–Usability Effect | Consistent tokens and spacing increase trust on a money screen. No visual noise near the amount. |
| L10 | Von Restorff Effect | Exactly one element stands out: the total amount or the pay button. Not both at once. |
| L11 | Postel's Law | Accept input leniently (spaces in phone numbers, trailing whitespace), send strict data to the backend. |
| L12 | Goal-Gradient Effect | Visible step indicator; later steps feel shorter. Do not add a step after payment. |
| L13 | Serial Position Effect | Most important information first and last on the result screen: status at the top, next action at the bottom. |
| L14 | Law of Proximity | Amount, currency, and VAT note sit together; shipping address sits apart. |
| L15 | Law of Common Region | Card or divider boundaries around each group: items, payment, totals. |
| L16 | Law of Similarity | All secondary actions share one style; the primary pay button has a unique style. |
| L17 | Law of Prägnanz | Prefer the simplest layout that works. One column on mobile. |
| L18 | Occam's Razor | Remove any field the backend does not need to approve. |
| L19 | Pareto Principle | Design the success path and the three most common failures (window closed, approve slow, approve failed) to perfection first. |
| L20 | Parkinson's Law | Checkout should feel complete in under one minute. Time-box the approve wait; after 10 s show "still working" with a safe exit. |
| L21 | Cognitive Load | No jargon ("merchantPayKey", "resultCode") in copy. Translate to buyer language. |
| L22 | Flow | Do not interrupt with modals during payment. Errors appear inline at the point of action. |
| L23 | Paradox of the Active User | Buyers do not read instructions. Defaults and labels must carry the task. |
| L24 | Selective Attention | One banner at a time. Do not stack promo and error messages. |

## Checklist to attach to every UI spec and UX flow

```
UX laws check
- Primary action per screen: 1 (L02, L10)
- Touch target ≥ 48 dp, thumb zone on mobile (L01)
- Feedback ≤ 400 ms on every action (L05)
- Success and failure end states designed, each with one next step (L06, L13)
- Resume path after abandoned Naver Pay window (L07)
- No technical terms in buyer copy (L21)
- Groups ≤ 7, items collapsed > 3 (L04, L14, L15)
- Laws cited for every non-obvious choice: L__, L__
```
