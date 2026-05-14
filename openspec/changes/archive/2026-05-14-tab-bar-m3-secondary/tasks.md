## 1. CSS — Tab container

- [x] 1.1 Remove `background` from `.tab-bar` and replace with `border-bottom: 1px solid rgba(255,255,255,0.12)` (M3 outline-variant divider)
- [x] 1.2 Set container height: `min-height: 48px` on `.tab-bar`

## 2. CSS — Tab button: typography and colours

- [x] 2.1 Set `font-size: 14px` and `font-weight: 500` on `.tab-btn`
- [x] 2.2 Set inactive label colour to `rgba(255,255,255,0.60)` on `.tab-btn`
- [x] 2.3 Set active label colour to `#ffffff` on `.tab-btn.active` (remove blue colour)

## 3. CSS — Active indicator

- [x] 3.1 Remove `border-bottom: 3px solid #3b82f6` from `.tab-btn.active`
- [x] 3.2 Add indicator via `::after` on `.tab-btn.active`: `height: 2px`, `width: 100%`, `background: #3b82f6`, `border-radius: 3px 3px 0 0`, positioned at `bottom: 0`

## 4. CSS — State layers (hover / press)

- [x] 4.1 Replace `transform: translateY(-2px)` on `.tab-btn:hover` with `background: rgba(255,255,255,0.08)` and `transform: none`
- [x] 4.2 Add `.tab-btn:active { background: rgba(255,255,255,0.12); transform: none; }`
- [x] 4.3 Ensure `border-bottom` on `.tab-btn:hover` remains `transparent` (no indicator on inactive tabs on hover)

## 5. Finalisation

- [x] 5.1 Bump `CACHE_NAME` in `sw.js` (v5 → v6)
- [x] 5.2 Manual verification: open index.html, check tab bar against M3 metrics (white active label, 2px blue indicator, grey divider, state layer on hover)
