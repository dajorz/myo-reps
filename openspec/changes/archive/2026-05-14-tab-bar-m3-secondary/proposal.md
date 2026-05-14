## Why

The current tab bar uses a custom visual style that follows no recognised design system: the active tab applies the primary blue colour to both the label and the bottom border, which is inconsistent with Material Design 3. M3 Secondary Tabs are the correct pattern for this use case — navigating between two equivalent-level views — and bring a more polished, coherent, and recognisable aesthetic.

## What Changes

- **BREAKING** Active label colour changes from `#3b82f6` (Primary) to `on-surface` (white) — more legible and correct per M3
- Reduce active indicator thickness from 3px to 2dp (M3 Secondary spec)
- Add `border-radius: 3px 3px 0 0` to active indicator (M3 shape token)
- Add 1dp divider at the bottom of the container, separate from the indicator
- Adjust label typography to Title Small: 14sp, font-weight 500
- Adjust tab container height to 48dp (M3 spec: label-only container height)
- Replace permanent semi-transparent background on active tab with a subtle state layer on hover/press only
- Adjust inactive label colour to `on-surface-variant` (~60% white opacity in dark theme)

## Capabilities

### New Capabilities
_(none — this change is purely visual)_

### Modified Capabilities
- `tab-navigation`: Visual requirements for the tab bar change — label colours, height, active indicator (thickness, shape, colour), hover state, and divider must align with M3 Secondary Tabs

## Impact

- **index.html** (inline CSS): Only affects the `.tab-bar` and `.tab-btn` CSS block — no impact on JS, HTML structure, or i18n
- **sw.js**: Cache version bump required
