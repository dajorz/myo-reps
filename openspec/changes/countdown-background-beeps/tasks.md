## 1. State refactor — timestamp-based countdown

- [ ] 1.1 Add module-level variables in the Timer tab state block: `timerCountdownEndTime` (number, ms), `timerCountdownWorker` (Worker | null), `timerKeepaliveOscillator` (OscillatorNode | null), `timerKeepaliveGain` (GainNode | null).
- [ ] 1.2 Replace decrement-based remaining in `startTimerCountdown(seconds)`: compute `timerCountdownEndTime = Date.now() + seconds * 1000` and derive `timerCountdownRemaining` from `Math.max(0, Math.ceil((timerCountdownEndTime - Date.now()) / 1000))` on every tick.
- [ ] 1.3 Track previously-displayed integer seconds in a local variable so beeps only fire on each integer transition (not multiple times per second when the worker ticks every 250 ms).

## 2. Web Worker for ticks

- [ ] 2.1 Add a helper `createTimerWorker()` that builds an inline Worker via `Blob(['setInterval(()=>postMessage(0),250)'], { type: 'application/javascript' })` and `URL.createObjectURL(blob)`. Revoke the object URL after worker construction.
- [ ] 2.2 In `startTimerCountdown`, create the worker, store it in `timerCountdownWorker`, and wire its `onmessage` to a new `onTimerTick()` function that contains the per-tick logic (update display, update ring, fire beeps on integer-second transitions, detect finish).
- [ ] 2.3 In `timerReturnToIdle`, terminate the worker (`timerCountdownWorker.terminate()`) and null it.
- [ ] 2.4 Remove the existing `setInterval(timerCountdownInterval)` calls from `startTimerCountdown` and `timerPauseResume` (Resume branch). Keep `timerCountdownInterval` only if still needed for backwards-compat checks; otherwise remove it.

## 3. AudioContext keepalive

- [ ] 3.1 Add a helper `startTimerKeepalive()` that ensures `audioContext` is initialised (call `initAudioContext()`), creates an `OscillatorNode` + `GainNode` with `gain = 0.0001`, connects oscillator → gain → destination, starts the oscillator, and stores both nodes in module variables.
- [ ] 3.2 Add a helper `stopTimerKeepalive()` that, if a keepalive oscillator exists, calls `.stop()` on it, disconnects both nodes, and nulls both variables. Wrap in try/catch (oscillator may already be stopped).
- [ ] 3.3 Call `startTimerKeepalive()` at the start of `startTimerCountdown` and at the resume branch of `timerPauseResume`.
- [ ] 3.4 Call `stopTimerKeepalive()` inside `timerReturnToIdle` and at the pause branch of `timerPauseResume`.

## 4. Pause / Resume / Stop / Restart wiring

- [ ] 4.1 In `timerPauseResume` Pause branch: terminate worker, stop keepalive, store the current `timerCountdownRemaining` (already derived from `endTime`).
- [ ] 4.2 In `timerPauseResume` Resume branch: set `timerCountdownEndTime = Date.now() + timerCountdownRemaining * 1000`, start keepalive, create a new worker.
- [ ] 4.3 Verify `timerStop` calls `timerReturnToIdle` (already does) so worker + keepalive are cleaned up.
- [ ] 4.4 Verify `timerRestart` cleans up before re-arming by relying on `startTimerCountdown`'s teardown of any previous worker / keepalive at the start of the function.
- [ ] 4.5 Add a guard at the top of `startTimerCountdown` that, if a worker / keepalive already exists, tears them down before creating new ones.

## 5. Visibility catch-up

- [ ] 5.1 Add a `document.addEventListener('visibilitychange', ...)` handler that, when `document.visibilityState === 'visible'` and a countdown is active (`timerCountdownEndTime > 0` and not paused), recomputes remaining, updates display + ring, and if `remaining <= 0` calls the same finish path used on tick (play finish beep if not already played, then `timerReturnToIdle()`).
- [ ] 5.2 Use a flag `timerFinishBeepPlayed` to avoid double-playing the finish beep when both the worker tick and the visibilitychange handler reach zero.

## 6. Manual verification on Android Chrome

- [ ] 6.1 Open `index.html` (installed PWA) on Android, start a 30 s timer, switch to another app immediately, and confirm the 3-2-1 beeps and finish beep play at approximately the right time.
- [ ] 6.2 Repeat with a 60 s timer, switching away halfway through; return to the tab and confirm remaining time matches wall-clock (within ±1 s).
- [ ] 6.3 Test Pause / Resume / Stop / Restart while in the foreground — behaviour unchanged from before.
- [ ] 6.4 Pause a timer, switch apps for 30 s, return, and confirm the timer is still paused at the same remaining value.
- [ ] 6.5 Confirm no audible artefact from the keepalive oscillator (no clicks/hiss) on device speaker and on Bluetooth headphones.
- [ ] 6.6 Confirm Myo-Reps rest timer still works exactly as before (no regression on the unchanged path).

## 7. Service worker cache

- [ ] 7.1 Bump `CACHE_NAME` in `sw.js` (e.g., increment patch version).
- [ ] 7.2 Reload the PWA twice on device to confirm the new `index.html` is picked up by the service worker.
