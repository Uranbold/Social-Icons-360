---
name: pro-design
description: Professional design process for the Naver Pay checkout. Use for any UI, UX, or interaction design work - screens, components, tokens, flows, copy, motion, feedback timing, design review. Chains the artifact-design fundamentals, the Figma design skills, and the Laws of UX checklist in docs/naverpay/design/UX_LAWS.md.
---

# Pro design process

Run these steps in order. Skipping a step is a defect in the deliverable.

## 1. Load the foundations

- Invoke the `artifact-design` skill for layout, typography, colour, theming, and
  responsive fundamentals. Apply its contract (tokens on `:root`, light and dark
  themes, phone-width layout with 16 px gutters) to every screen spec and prototype.
- If the work produces a chart, meter, or KPI tile (for example an admin
  reconciliation view), also invoke `dataviz`.
- For Figma work, call `mcp__Figma__get_figma_skill` for the matching skill before
  calling `use_figma`:
  - `figma-use` for any edit in Figma
  - `figma-generate-design` to push a built page into Figma
  - `figma-generate-library` to build the token and component library
  - `figma-code-connect` to map Figma components to code for `front` and `mobile`
- Read `docs/naverpay/design/UX_LAWS.md`. Every recommendation cites a law ID.
- For motion, timing, gestures, or focus behaviour, read
  `docs/naverpay/design/MOTION.md` and use its tokens; `ix` owns that file.

## 2. Understand before drawing

- Restate the buyer goal for the screen in one sentence.
- List the states: default, loading, empty, error, success, and the Naver Pay
  specific ones from `ARCHITECTURE.md` §2 (window closed, approve slow, approve
  failed, amount mismatch, duplicate return).
- Name the one primary action (L02, L10).

## 3. Design

- Tokens first. Colour, type scale, spacing, radius, elevation, motion; both themes.
  No literal values in a component spec.
- Hierarchy: amount or pay button is the single focal point (L10). Group with
  proximity and common region (L14, L15).
- Korean-first typography: Hangul-capable stack, line-height tuned for mixed Hangul,
  Latin, and numerals; KRW with thousands separators, no decimals.
- Motion: durations and easings only from `MOTION.md`; feedback under 400 ms
  (L05); reduced-motion variant for every animation; one loading indicator at a
  time. Interaction specs follow the template in `.claude/agents/ix.md`.

## 4. Verify

- Contrast: text ≥ 4.5:1, large text and controls ≥ 3:1, both themes. Record pairs.
- Touch targets ≥ 48 dp (L01). Keyboard order and visible focus on web.
- Run the UX laws checklist from `UX_LAWS.md` and paste the filled checklist into
  the spec or flow file.
- Heuristic pass with Nielsen's 10 heuristics on the complete flow; file findings
  with severity 0–4 and a proposed fix.

## 5. Hand off

- Specs in `docs/naverpay/ui/components/<name>.md`, flows in
  `docs/naverpay/ux/flows/<name>.md`, copy in `docs/naverpay/ux/copy.md`,
  interaction specs in `docs/naverpay/ix/<name>.md`.
- Each file ends with the filled UX laws checklist and the contrast table.
- Tell `front` and `mobile` which files changed and which tokens were added.
