## 1. Tab Navigation Structure

- [x] 1.1 Add tab bar HTML (two buttons: Myo-Reps, Timer) and CSS styles (flex row, active state highlight, dark theme)
- [x] 1.2 Wrap existing Myo-Reps content (config-panel, container, language-flags) in a `div.tab-content[data-tab="myo-reps"]`
- [x] 1.3 Add new `div.tab-content[data-tab="timer"]` with two-section layout: preset grid (top) and countdown area (bottom)
- [x] 1.4 Implement `switchTab(tabName)` function that toggles `.hidden` class on tab contents and updates active tab highlight
- [x] 1.5 Add i18n keys for tab labels (`tabMyoReps`, `tabTimer`) in both EN and ES translations

## 2. Swipe Gesture Navigation

- [x] 2.1 Add pointer event listeners (`pointerdown`, `pointermove`, `pointerup`) on body to track swipe start/end coordinates
- [x] 2.2 Implement swipe detection logic: minimum 50px horizontal delta, horizontal/vertical ratio > 1.5
- [x] 2.3 Wire swipe-left to switch to next tab and swipe-right to switch to previous tab

## 3. Timer Preset Grid

- [x] 3.1 Define default presets array `[30, 60, 90, 120, 150, 180]` and load/save functions for `myoRepsTimerPresets` localStorage key
- [x] 3.2 Add CSS for timer preset grid (responsive grid, large touch-target buttons, dark theme consistent, disabled/dimmed state)
- [x] 3.3 Implement `renderTimerPresets()` function that builds the grid of preset buttons with M:SS formatted labels, always sorted ascending
- [x] 3.4 Add "+" button to the grid for adding new presets (hidden when 10 presets reached)

## 4. Timer Countdown with Circular Progress

- [x] 4.1 Add SVG circular progress ring markup (circle with `stroke-dasharray`/`stroke-dashoffset`) and CSS for the countdown area
- [x] 4.2 Implement `startTimerCountdown(seconds)` function that starts countdown, updates number and SVG ring each second
- [x] 4.3 Implement `updateTimerProgress(remaining, total)` to calculate and apply `stroke-dashoffset` to the SVG ring
- [x] 4.4 Wire countdown to call existing `playCountdownBeep()` at 3, 2, 1 seconds and `playFinishBeep()` at 0
- [x] 4.5 On countdown completion (0 seconds), return to idle state and re-enable presets

## 5. Timer Controls (Pause / Stop / Restart)

- [x] 5.1 Add Pause, Stop, and Restart button HTML and CSS inside the countdown area
- [x] 5.2 Implement Pause/Resume toggle: pause freezes countdown and ring, Resume button resumes from paused time
- [x] 5.3 Implement Stop: cancel countdown, reset countdown area to idle placeholder, re-enable presets
- [x] 5.4 Implement Restart: reset countdown to original preset duration, reset ring to full, continue running
- [x] 5.5 Disable (dim) all preset buttons when countdown is active, re-enable on stop/completion

## 6. Preset Management (Add/Edit/Delete)

- [x] 6.1 Implement add-preset flow: tap "+" → show inline input for seconds → validate (positive integer, max 10 presets) → append to presets array → sort ascending → save to localStorage → re-render grid
- [x] 6.2 Implement long-press detection on preset buttons (500ms threshold)
- [x] 6.3 Implement edit/delete UI on long-press: show input pre-filled with current seconds and a delete button
- [x] 6.4 Wire edit confirmation to update preset in array → sort → save → re-render
- [x] 6.5 Wire delete to remove preset from array → save → re-render

## 7. Persistence and State

- [x] 7.1 Save active tab to localStorage (`myoRepsActiveTab` key) in `switchTab()`
- [x] 7.2 Restore active tab from localStorage on app load in `loadSession()`

## 8. Internationalization

- [x] 8.1 Add all Timer tab translation keys to both EN and ES objects (tab labels, preset labels, add/edit/delete buttons, pause/stop/restart/resume buttons, input placeholders, idle state text)
- [x] 8.2 Update `updateUI()` to also refresh Timer tab text and control buttons when language changes

## 9. Finalization

- [x] 9.1 Bump `CACHE_NAME` version in `sw.js`
- [x] 9.2 Manual verification: open index.html, test tab switching, swipe, timer countdown with circular progress, pause/stop/restart, preset CRUD, preset limit (10 max), language toggle, and offline mode

## 10. Bug Fixes

- [x] 10.1 Fix footer positioning in Timer tab: ensure footer stays pinned at the bottom of the viewport, matching Myo-Reps tab layout
- [x] 10.2 Fix preset buttons not entering disabled/greyed state on first click when countdown starts — only on second click
