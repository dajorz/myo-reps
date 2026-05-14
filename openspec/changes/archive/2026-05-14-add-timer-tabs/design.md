## Context

The Myo Reps Gym Tracker is a single-file PWA (index.html) with inline CSS and JS. It currently has one workflow: tracking Myo-Reps sets via a state machine (activation → rest → mini-series). All UI is rendered in a single full-screen layout with no concept of pages or tabs. The app uses localStorage for persistence, Web Audio API for beeps, and a translations object for EN/ES i18n.

This change introduces a tab system to host the existing Myo-Reps tracker alongside a new standalone countdown Timer.

## Goals / Non-Goals

**Goals:**
- Add a lightweight tab bar with two tabs (Myo-Reps, Timer) without external dependencies
- Enable swipe gesture navigation between tabs anywhere on the screen
- Provide preconfigured countdown timer presets (30s, 60s, 90s, 120s, 150s, 180s)
- Allow users to add, edit, and delete custom timer presets (max 10)
- Persist custom presets in localStorage
- Reuse existing audio system for timer countdown beeps
- Maintain full bilingual support (EN/ES)
- Single-screen timer layout: preset grid + active countdown visible together
- Circular progress indicator around the countdown number

**Non-Goals:**
- Simultaneous timers running across tabs (only one active timer at a time)
- Complex animation or transition effects between tabs
- Refactoring existing Myo-Reps code — it stays as-is, just wrapped in a tab container
- Adding more than two tabs in this change
- Drag-and-drop reordering of presets

## Decisions

### 1. Tab implementation: CSS-based show/hide with wrapper divs

Wrap existing Myo-Reps content in a `div.tab-content[data-tab="myo-reps"]` and create a new `div.tab-content[data-tab="timer"]`. Switch visibility via a `.hidden` class on the inactive tab. The tab bar is a flex row of buttons at the top.

**Why**: Simplest approach with zero dependencies. Keeps both tabs in the DOM so state is preserved when switching. No routing library needed.

**Alternative considered**: Using `display: none` with inline styles — rejected because the app already uses a `.hidden` utility class.

### 2. Swipe detection: pointer events with threshold

Use `pointerdown`/`pointermove`/`pointerup` events on the body to detect horizontal swipes. A minimum horizontal delta of 50px with a horizontal-to-vertical ratio > 1.5 qualifies as a swipe. This avoids false triggers from vertical scrolling.

**Why**: Pointer events work across touch and mouse. No library needed. The ratio threshold prevents conflicts with vertical scroll.

**Alternative considered**: Touch events only — rejected because pointer events are more universal and the app targets modern browsers.

### 3. Timer presets: stored as a sorted array in localStorage

Default presets: `[30, 60, 90, 120, 150, 180]`. User customizations stored under a new `myoRepsTimerPresets` localStorage key. When present, it fully replaces the defaults. Presets are always displayed sorted ascending by duration. Maximum 10 presets enforced.

**Why**: Simple flat array of seconds. Full replacement semantics avoids merge complexity. Fixed ascending sort keeps UI predictable without needing drag-and-drop.

### 4. Timer UI: unified single-screen layout (grid top + countdown bottom)

The Timer tab always shows both the preset grid (top) and the countdown area (bottom) on the same screen. When no timer is active, the countdown area shows a placeholder/idle state. When active, it shows the circular progress ring with the countdown number and control buttons (Pause, Stop, Restart). Preset buttons are disabled (visually dimmed) during an active countdown to prevent switching timers mid-count.

**Why**: No view switching means less disorientation in a gym context. Grid stays visible so the user always sees their options. Disabling presets during countdown prevents accidental restarts.

**Alternative considered**: Toggle between grid view and countdown view — rejected because user specifically requested everything on one screen.

### 5. Circular progress indicator: SVG ring

Use an SVG circle with `stroke-dasharray`/`stroke-dashoffset` to draw a progress ring around the countdown number. The ring depletes as time passes. Lightweight, no canvas needed, animates smoothly with CSS transitions on `stroke-dashoffset`.

**Why**: Pure SVG + CSS, no external library. Visually clear progress at a glance. Works well in dark theme with a colored stroke (e.g., blue → red gradient as time runs out).

### 6. Timer controls: Pause + Stop + Restart

Three control buttons during active countdown:
- **Pause/Resume**: Toggles the countdown. Pausing keeps the current time and ring position.
- **Stop**: Cancels the countdown and resets to idle state. Presets re-enable.
- **Restart**: Resets the countdown to the original preset duration and starts over.

**Why**: Full control set covers all gym scenarios (pause for a quick interruption, stop to abandon, restart if you lost count). Consistent with the Myo-Reps rest timer having pause/cancel.

### 7. Audio reuse: share `playCountdownBeep()` and `playFinishBeep()`

The existing audio functions work standalone. The Timer tab calls the same functions at the same thresholds (beep at 3-2-1, triple beep at 0).

**Why**: Code reuse; consistent audio experience across both tabs.

## Risks / Trade-offs

- **Single-file complexity**: Adding tab system + timer + SVG progress increases index.html size. → Mitigated by keeping timer logic minimal and well-sectioned with clear comments.
- **Swipe conflicts**: Swipe gestures could interfere with future horizontal UI elements. → Mitigated by threshold tuning and limiting detection to the main content area.
- **Myo-Reps timer still running on tab switch**: If user starts a rest timer in Myo-Reps, switching to Timer tab doesn't pause it. → Acceptable because the audio beeps will still fire. This is a feature, not a bug.
- **Disabled presets UX**: Users might try to tap a preset during countdown and be confused. → Mitigated by clear visual dimming and the grid staying visible (they can see it's disabled).
- **localStorage key proliferation**: New keys `myoRepsTimerPresets` and `myoRepsActiveTab` alongside existing `myoRepsSession` and `myoRepsLanguage`. → Acceptable for a single-file app.
