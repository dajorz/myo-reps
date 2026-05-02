# Myo Reps Gym Tracker

Single-page PWA for tracking Myo Reps workout sessions. No build step, no dependencies.

## Tech Stack

- Pure HTML/CSS/JavaScript (single `index.html`)
- Service Worker (`sw.js`) for offline caching
- Web App Manifest (`manifest.json`) for installability
- `localStorage` for persistence

## Architecture

All application logic lives in `index.html`:
- **CSS**: Inline `<style>` block — dark theme, mobile-first, portrait-optimized
- **HTML**: Config panel, counter, state indicator, timer, action buttons, language flags
- **JavaScript**: Inline `<script>` — state machine (`activation → rest → mini-series`), timer, Web Audio API beeps, i18n

## Conventions

- **No build tools** — open `index.html` directly in a browser to test
- **Bilingual UI** — English and Spanish via `translations` object; all user-facing strings must use `t('key')`
- **Service Worker cache** — bump `CACHE_NAME` version in `sw.js` when changing any cached asset
- **Mobile-first design** — max-width constraints, large touch targets, portrait layout
- **State machine** — app state is one of: `activation`, `rest`, `mini-series`

## Testing

No automated tests. Verify manually by opening `index.html` in a browser. Check:
1. Timer counts down and plays audio beeps at 3-2-1-0
2. Mini-series counter increments after each rest completes
3. Language toggle switches all visible text
4. Works offline after first load (service worker)

## Key Patterns

- DOM updates go through `updateUI()` for language-aware rendering
- Audio uses singleton `AudioContext` initialized on first user gesture
- `saveSession()` / `loadSession()` handle `localStorage` round-trip
