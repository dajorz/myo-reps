## Why

When the Timer tab is running a countdown on Chrome for Android and the user switches to another app (e.g., Spotify, WhatsApp), the timer effectively stops: `setInterval` ticks get throttled or paused, the `AudioContext` is suspended, and the 3-2-1 / finish beeps never play. Users rely on those audible cues to time gym sets without staring at the phone, so this breaks the core use case of the Timer tab.

## What Changes

- Replace the tick-based countdown with a **timestamp-based** schedule (`endTime = Date.now() + ms`) so remaining time is always computed from the wall clock, not accumulated ticks.
- Drive ticks from a **Web Worker** (created inline via Blob URL) instead of the main thread's `setInterval`, since Chrome on Android throttles main-thread timers in background tabs but worker timers run much more reliably.
- Keep the `AudioContext` alive in background by playing a **near-silent oscillator** for the duration of the countdown, preventing Chrome from suspending audio output when the tab loses visibility.
- Add a `visibilitychange` listener that recomputes the display and fires any missed terminal events (e.g., finish beep) the moment the user returns to the tab.
- Scope: only the **Timer tab** countdown. The Myo-Reps rest timer is unchanged — the user keeps that one in the foreground.

## Capabilities

### New Capabilities
_None._

### Modified Capabilities
- `countdown-timer`: the existing "Audio alerts during countdown" requirement must hold even when the tab is in the background or the device has switched to another app. New requirement covering background reliability.

## Impact

- **Code**: `index.html` only — the timer countdown functions (`startTimerCountdown`, `timerPauseResume`, `timerStop`, `timerRestart`, `timerReturnToIdle`) and a small inline Web Worker. Audio helper functions (`playCountdownBeep`, `playFinishBeep`) are unchanged.
- **Service worker**: bump `CACHE_NAME` in `sw.js` per project convention since `index.html` changes.
- **Dependencies**: none added (vanilla Web Worker + Web Audio API).
- **Manifest / permissions**: none. No notifications, no Wake Lock required.
- **Myo-Reps rest timer**: not affected.
- **Browser support**: relies on Web Workers and Web Audio API, both available in every browser the PWA already targets.
