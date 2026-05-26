## Context

The Timer tab in `index.html` runs a countdown via `setInterval(fn, 1000)` on the main thread. The interval handler decrements `timerCountdownRemaining`, redraws the SVG ring, and calls `playCountdownBeep()` / `playFinishBeep()` (Web Audio API oscillators) at 3-2-1 / 0 seconds.

On Chrome for Android, when the tab loses visibility (user switches to another app), two things conspire to break the timer:

1. **`setInterval` is throttled or paused.** Background tabs in Chrome get aggressive timer throttling — intervals can fire every few seconds or stop entirely after ~5 minutes.
2. **`AudioContext` is auto-suspended.** Without an active audio stream, Chrome suspends the context to save battery, so even if a tick did fire and call `createOscillator()`, nothing would be audible.

The Myo-Reps rest timer uses the same pattern, but the user keeps that screen visible (short rests), so it's not affected and is out of scope.

The codebase has hard constraints: single `index.html`, no build step, no external dependencies, bilingual strings via `t('key')`, service worker cache bumped on any cached-asset change.

## Goals / Non-Goals

**Goals:**

- The 3-2-1 countdown beeps and finish beep play at approximately the correct wall-clock time even when the Timer tab is backgrounded by switching to another Android app.
- Remaining time displayed in the ring is correct (within ±1s of wall-clock) when the user returns to the tab.
- Pause / Stop / Restart continue to behave as today.
- Zero new dependencies; everything stays in `index.html`.
- Battery impact is negligible (oscillator gain near zero, worker tick ≤ 250ms).

**Non-Goals:**

- Fixing the Myo-Reps rest timer (the user keeps it foreground).
- OS-level notifications when the timer finishes (not requested).
- Keeping the screen awake (Wake Lock not required).
- Resilience against the OS killing the Chrome process entirely (out of our control).
- Surviving a full app close + reopen mid-countdown (no persistence requirement).

## Decisions

### Decision 1: Timestamp-based scheduling, not tick accumulation

Store `timerCountdownEndTime = Date.now() + remainingMs` when the countdown starts (and recompute on resume). On every tick, derive `remaining = Math.max(0, Math.ceil((timerCountdownEndTime - Date.now()) / 1000))`.

**Why:** Even if ticks are throttled or dropped, the displayed and logical remaining time stays correct based on the wall clock. When the user returns to the tab, the display is right immediately.

**Alternative considered:** Keep `remaining--`. Rejected because dropped ticks produce drift — the timer would visibly "lag" after coming back from background.

### Decision 2: Web Worker drives the ticks

Create an inline Web Worker (via `Blob` + `URL.createObjectURL`) that runs `setInterval(() => postMessage('tick'), 250)`. The main thread listens for `tick` messages and runs the existing display / beep / finish logic.

**Why:** Chrome on Android throttles main-thread timers in background tabs but allows worker timers to keep firing at near-normal cadence. 250ms cadence keeps the countdown number accurate (max 250ms late on each second boundary) and lets us catch the 3-2-1 transitions even if a tick is briefly delayed.

**Alternatives considered:**
- `setTimeout` chain on main thread: same throttling problem.
- `requestAnimationFrame`: stops entirely when tab is hidden.
- Service worker with `setTimeout`: service workers can be killed at any time; not reliable for short-lived timers, and message passing is more cumbersome.

Worker is created inline (no extra file) to respect the single-file convention.

### Decision 3: Silent keepalive oscillator to prevent AudioContext suspension

When the countdown starts, create an oscillator with `gain = 0.0001` connected to `audioContext.destination` and start it. Stop it when the countdown ends, is stopped, or returns to idle.

**Why:** With an active audio stream, Chrome keeps the `AudioContext` running and the tab classified as audible, which (a) prevents context suspension so subsequent `createOscillator()` calls produce sound, and (b) further reduces background throttling.

**Alternative considered:** Resume the AudioContext on every beep. Rejected because by the time we know we need a beep, the context is suspended, the `resume()` call returns a promise that may resolve too late, and on some Android versions resume only works on a user gesture.

**Trade-off:** Battery and audio focus. Gain `0.0001` is inaudible (well below noise floor) and the oscillator only runs while a countdown is active. Audio focus may briefly duck other apps' audio when starting/stopping the keepalive — acceptable for a gym timer scenario.

### Decision 4: `visibilitychange` handler for catch-up on return

On `visibilitychange` to visible while a countdown is active: recompute remaining from `endTime`, update the display + ring immediately, and if `remaining <= 0` trigger the same finish path that a tick would (finish beep + `timerReturnToIdle()`).

**Why:** Belt-and-suspenders. Even if the worker tick fired correctly and beeps already played in background, this ensures the UI shows the right state instantly. If for some reason the worker was paused entirely, this handler still terminates the timer cleanly.

### Decision 5: Pause semantics

Pause stores the remaining seconds (computed from `endTime - Date.now()`), stops the worker, and stops the keepalive oscillator. Resume sets `endTime = Date.now() + remainingMs`, starts a new worker and keepalive oscillator.

**Why:** Symmetric with Decision 1. Avoids any drift accumulated during long pauses and keeps the "in-flight" resources scoped to "countdown actively running."

### Decision 6: Reuse existing audio helpers unchanged

`playCountdownBeep()` and `playFinishBeep()` are not modified. They already lazy-init the `AudioContext` and resume it if suspended. The keepalive oscillator just makes resume reliable when called from a backgrounded tab.

**Why:** Minimize the diff; isolate background handling to the timer flow.

## Risks / Trade-offs

- **[Aggressive OEM battery savers (Xiaomi, Samsung One UI, Huawei)]** → May still kill background tabs more aggressively than stock Android. Mitigation: this is a known PWA limitation, and the user has the app installed (which gets better treatment than a tab). Document in tasks as a known limitation if encountered during manual testing.
- **[Audio focus interruption]** → A near-silent oscillator may briefly claim audio focus and affect media playback in other apps. Mitigation: only active while a countdown is running, and gain is so low no audio output is produced. Verify during manual testing that Spotify/YouTube playback is not visibly affected when starting a timer.
- **[Inline Worker via Blob URL and CSP]** → If a stricter CSP is added later, `blob:` workers may be blocked. Mitigation: no CSP is currently set; document the dependency in design so a future CSP change considers it.
- **[Worker leak on errors]** → If the main thread throws after creating the worker but before assigning the cleanup handle, the worker would leak. Mitigation: assign worker + oscillator to module-level variables immediately on creation, always tear down in `timerReturnToIdle()`.
- **[`Date.now()` jumps if the OS clock changes]** → Highly unlikely during a 30-180s countdown; not worth defending against. Could be revisited with `performance.now()` if it ever becomes a real issue.
- **[Service worker cache invalidation]** → `index.html` changes; users on the old cached version will see the bug until the SW updates. Mitigation: bump `CACHE_NAME` per project convention (already in tasks).
