## Context

The tab bar was implemented in the `add-timer-tabs` change with a custom visual style. When reviewed in real use, the design follows no established reference system: the active tab colours both the label and the bottom border in primary blue (`#3b82f6`), there is a permanent semi-transparent background on the container, and neither the typography nor the height follow standard metrics.

Material Design 3 Secondary Tabs is the correct pattern for this case (equivalent-level navigation, text-only labels, single row). This change applies only the M3 design tokens to the tab bar CSS block — no HTML, JS, or i18n changes.

**M3 Secondary Tabs — relevant tokens (dark theme):**

| M3 Token            | Adapted dark theme value              |
|---------------------|---------------------------------------|
| Container height    | 48dp                                  |
| Active label color  | on-surface → `#ffffff`              |
| Inactive label      | on-surface-variant → `rgba(255,255,255,0.60)` |
| Active indicator    | Primary → `#3b82f6`, height 2dp, shape 3 3 0 0 |
| Divider             | outline-variant → `rgba(255,255,255,0.12)`, 1dp |
| State layer (hover) | on-surface 8% → `rgba(255,255,255,0.08)` |
| State layer (press) | on-surface 12% → `rgba(255,255,255,0.12)` |
| Typography          | Title Small: 14sp, weight 500         |

## Goals / Non-Goals

**Goals:**
- Align the tab bar with M3 Secondary Tabs: height, indicator, colours, and typography
- Active label in `on-surface` (white), not Primary (blue)
- Active indicator: 2dp height, full tab width, shape `3px 3px 0 0`
- 1dp divider below the container (separate from the indicator)
- Subtle state layer on hover/press, no permanent fill on active tab
- Pure CSS — no external dependencies

**Non-Goals:**
- Changing JS behaviour (`switchTab`, swipe) — no functional changes
- Adding icons to tabs
- Sliding indicator transition animation between tabs
- Scrollable tabs support

## Decisions

### 1. Indicator as `::after` pseudo-element instead of `border-bottom`

The current `border-bottom` is part of the box model and does not allow controlling `border-radius` only on the top corners (3,3,0,0). With `::after` the 2px indicator can be positioned freely with `border-radius: 3px 3px 0 0`.

**Alternative discarded**: inset `box-shadow` — does not support arbitrary border-radius on a single side.

### 2. Remove permanent `background` from the container

M3 Secondary Tabs use `Surface` as the container background, with no active tint. The active state is communicated **only** through the indicator and the label colour. The current `background: rgba(0,0,0,0.35)` will be replaced by the app surface colour (`transparent` — the `body` gradient already acts as background).

### 3. State layer via `background` on hover/press of `.tab-btn`

M3 uses state layers (semi-transparent colour overlays) for hover (8% on-surface) and press (12% on-surface). Implemented with `background` on `:hover` and `:active` instead of the current `transform: translateY(-2px)` — tabs must not move vertically.

## Risks / Trade-offs

- **Inactive label contrast**: `rgba(255,255,255,0.60)` on dark background yields a contrast ratio of ~7:1 — within WCAG AA. → Acceptable.
- **Indicator with `::after`**: If `.tab-btn` has `overflow: hidden`, the indicator would be clipped. → Mitigated by ensuring `overflow: visible` (default) on `.tab-btn`.
