## ADDED Requirements

### Requirement: Countdown beeps play when the tab is backgrounded
The Timer tab countdown SHALL continue producing the 3-2-1 countdown beeps and the finish beep at the correct wall-clock times when the user switches to another app on Android (the tab loses visibility). The implementation SHALL use a Web Worker to drive ticks and SHALL keep the `AudioContext` from being suspended for the duration of the countdown.

#### Scenario: Beeps fire after switching to another app
- **WHEN** the user starts a countdown on the Timer tab
- **AND** switches to another Android app before the countdown ends
- **THEN** the 3-2-1 countdown beeps play at approximately the correct wall-clock times
- **AND** the finish beep plays when the countdown reaches zero

#### Scenario: Finish beep fires even if app is in background at zero
- **WHEN** a countdown is running and the tab is not visible
- **AND** the wall-clock time reaches zero
- **THEN** the finish beep plays
- **AND** the timer transitions to idle (controls hidden, presets re-enabled) by the time the user returns to the tab

### Requirement: Remaining time stays correct across visibility changes
The Timer tab SHALL display the correct remaining seconds (within ±1 second of wall-clock) immediately when the user returns to the tab after backgrounding the app during an active countdown. Computation SHALL be based on a stored end timestamp, not on accumulated ticks.

#### Scenario: Remaining time on return from background
- **WHEN** a 60-second countdown is running
- **AND** the user switches to another app for 20 seconds and returns
- **THEN** the displayed remaining time is approximately 40 seconds (±1s)
- **AND** the progress ring matches the displayed time

#### Scenario: Countdown already expired during background
- **WHEN** a 30-second countdown is running
- **AND** the user switches to another app for 45 seconds and returns
- **THEN** the timer is in idle state (no controls, presets enabled, ring reset)
- **AND** the finish beep has already played (or plays immediately on return if it had not)

### Requirement: Background resources are released when countdown ends
The Web Worker and the AudioContext keepalive used to support background reliability SHALL be torn down whenever the countdown ends, is stopped by the user, or is paused. Resuming a paused countdown SHALL recreate them.

#### Scenario: Stop releases background resources
- **WHEN** a countdown is running
- **AND** the user taps Stop
- **THEN** the worker is terminated
- **AND** the keepalive oscillator is stopped

#### Scenario: Pause releases background resources
- **WHEN** a countdown is running
- **AND** the user taps Pause
- **THEN** the worker is terminated
- **AND** the keepalive oscillator is stopped

#### Scenario: Resume restores background resources
- **WHEN** a countdown is paused
- **AND** the user taps Resume
- **THEN** a new worker starts driving ticks
- **AND** a new keepalive oscillator starts

#### Scenario: Finish releases background resources
- **WHEN** a countdown reaches zero
- **THEN** the worker is terminated
- **AND** the keepalive oscillator is stopped
