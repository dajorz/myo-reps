## Why

The app currently only supports a single workflow (Myo-Reps tracking). Users doing gym sessions often need a simple countdown timer for rest periods between regular sets (non Myo-Reps exercises). Adding a tabbed interface with a dedicated Timer tab avoids switching to a separate app, keeping the user within the PWA for their entire workout.

## What Changes

- Add a horizontal tab navigation system at the top of the screen with swipe gesture support
- Keep all existing Myo-Reps functionality under the first tab ("Myo-Reps")
- Add a new "Timer" tab with preconfigured countdown timers (30s, 60s, 90s, 120s, 150s, 180s)
- Allow users to edit existing timer presets and add custom ones
- Persist custom timer configurations in localStorage
- Reuse the existing Web Audio API beep system for timer countdown alerts
- Support swipe gestures anywhere on the screen to switch between tabs

## Capabilities

### New Capabilities
- `tab-navigation`: Horizontal tab bar with swipe-based navigation between app sections
- `countdown-timer`: Standalone countdown timer with preconfigured and user-customizable presets, audio alerts, and localStorage persistence

### Modified Capabilities
_(none — no existing specs to modify)_

## Impact

- **index.html**: Structural changes to wrap existing content in a tab container; new HTML/CSS/JS for tab bar, swipe detection, and timer UI
- **sw.js**: Cache version bump required after changes
- **localStorage**: New key(s) for custom timer presets (existing `myoRepsSession` key unchanged)
- **i18n**: New translation keys for tab labels, timer UI strings, and button labels in both EN and ES
