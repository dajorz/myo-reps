## Requirements

### Requirement: Display preconfigured timer presets
The Timer tab SHALL display a grid of countdown timer buttons with default presets: 30s, 60s, 90s, 120s, 150s, 180s. Each button SHALL show the duration in a human-readable format (e.g., "1:30" for 90 seconds). The grid SHALL use large touch targets consistent with the app's mobile-first design. Presets SHALL always be displayed sorted in ascending order by duration.

#### Scenario: Timer tab shows default presets
- **WHEN** the user navigates to the Timer tab for the first time
- **THEN** six preset buttons are displayed: 30s, 60s, 90s, 120s, 150s, 180s
- **AND** they are sorted ascending by duration

#### Scenario: Presets display formatted time
- **WHEN** the Timer tab is visible
- **THEN** each preset button shows time in M:SS format (e.g., "0:30", "1:00", "2:30")

### Requirement: Unified single-screen layout
The Timer tab SHALL display the preset grid in the upper section and the countdown area in the lower section, both visible at all times. When no timer is active, the countdown area SHALL show an idle/placeholder state. When a timer is active, the countdown area SHALL show the circular progress ring, remaining time, and control buttons.

#### Scenario: Idle state
- **WHEN** no timer is running
- **THEN** the preset grid is visible at the top
- **AND** the countdown area below shows an idle placeholder

#### Scenario: Active timer state
- **WHEN** a timer is running
- **THEN** the preset grid remains visible (but disabled) at the top
- **AND** the countdown area shows the circular progress, time, and Pause/Stop/Restart buttons

### Requirement: Start countdown from preset
The app SHALL start a countdown when the user taps a preset button. The countdown SHALL display remaining time in large text inside a circular progress ring. Three control buttons SHALL appear: Pause, Stop, and Restart.

#### Scenario: Tap preset to start countdown
- **WHEN** the user taps the "1:00" preset button
- **THEN** a 60-second countdown begins
- **AND** the remaining time is displayed in large text inside a circular progress ring
- **AND** Pause, Stop, and Restart buttons are shown

### Requirement: Presets disabled during active countdown
All preset buttons SHALL be visually dimmed and non-interactive while a countdown is active. They SHALL re-enable when the countdown ends or is stopped.

#### Scenario: Tap preset during active countdown
- **WHEN** a countdown is running
- **AND** the user taps a preset button
- **THEN** nothing happens (the tap is ignored)
- **AND** the preset buttons appear dimmed

#### Scenario: Presets re-enable after stop
- **WHEN** the user stops an active countdown
- **THEN** all preset buttons become interactive again and are no longer dimmed

### Requirement: Circular progress indicator
The countdown area SHALL display an SVG circular progress ring around the remaining time number. The ring SHALL deplete proportionally as time passes (full ring at start, empty at zero).

#### Scenario: Progress ring at start
- **WHEN** a countdown starts
- **THEN** the progress ring is fully filled

#### Scenario: Progress ring at halfway
- **WHEN** half the countdown time has elapsed
- **THEN** the progress ring is approximately 50% depleted

#### Scenario: Progress ring at completion
- **WHEN** the countdown reaches 0
- **THEN** the progress ring is fully depleted

### Requirement: Pause and resume countdown
The user SHALL be able to pause a running countdown and resume it. While paused, the countdown number and progress ring SHALL freeze.

#### Scenario: Pause an active countdown
- **WHEN** the countdown is running
- **AND** the user taps the Pause button
- **THEN** the countdown pauses at the current time
- **AND** the Pause button changes to a Resume button

#### Scenario: Resume a paused countdown
- **WHEN** the countdown is paused
- **AND** the user taps the Resume button
- **THEN** the countdown resumes from where it was paused

### Requirement: Stop countdown
The user SHALL be able to stop (cancel) an active or paused countdown. Stopping SHALL reset the countdown area to its idle state and re-enable presets.

#### Scenario: Stop a running countdown
- **WHEN** a countdown is running
- **AND** the user taps the Stop button
- **THEN** the countdown stops
- **AND** the countdown area returns to idle state
- **AND** preset buttons re-enable

### Requirement: Restart countdown
The user SHALL be able to restart the current countdown from its original duration without returning to idle state.

#### Scenario: Restart a running countdown
- **WHEN** a countdown is running with 20 seconds left of a 60-second preset
- **AND** the user taps the Restart button
- **THEN** the countdown resets to 60 seconds and continues running
- **AND** the progress ring resets to full

### Requirement: Audio alerts during countdown
The app SHALL play countdown beeps at 3, 2, and 1 seconds remaining, and a finish beep at 0 seconds. The audio SHALL use the same Web Audio API functions as the Myo-Reps rest timer.

#### Scenario: Beeps at 3-2-1
- **WHEN** the countdown reaches 3, 2, or 1 seconds
- **THEN** a countdown beep plays (same as Myo-Reps rest beep)

#### Scenario: Finish beep at zero
- **WHEN** the countdown reaches 0 seconds
- **THEN** a triple finish beep plays (same as Myo-Reps finish beep)
- **AND** the countdown ends
- **AND** the countdown area returns to idle state
- **AND** preset buttons re-enable

### Requirement: Add custom timer preset
The user SHALL be able to add new timer presets by tapping an "add" button. The app SHALL display an inline input for entering the duration in seconds. The new preset SHALL appear in the grid sorted by duration. The app SHALL enforce a maximum of 10 presets.

#### Scenario: Add a custom preset
- **WHEN** the user taps the add button
- **THEN** an input field appears to enter seconds
- **WHEN** the user enters "45" and confirms
- **THEN** a 45-second preset button appears in the grid in its sorted position

#### Scenario: Reject invalid input
- **WHEN** the user enters a non-numeric or zero/negative value
- **THEN** the preset is not added

#### Scenario: Maximum preset limit reached
- **WHEN** there are already 10 presets
- **THEN** the add button is hidden or disabled

### Requirement: Edit existing timer preset
The user SHALL be able to modify the duration of any preset (default or custom). The edit interaction SHALL use a long-press gesture.

#### Scenario: Edit a preset duration
- **WHEN** the user long-presses a preset button
- **THEN** an input field appears pre-filled with the current duration in seconds
- **WHEN** the user changes the value to "45" and confirms
- **THEN** the preset updates to show "0:45" and re-sorts in the grid

### Requirement: Delete a timer preset
The user SHALL be able to delete any preset. A delete option SHALL be available during the edit interaction (long-press).

#### Scenario: Delete a preset
- **WHEN** the user long-presses a preset button
- **AND** taps the delete option
- **THEN** the preset is removed from the grid

### Requirement: Persist custom presets in localStorage
All timer presets (including modifications to defaults) SHALL be persisted in localStorage under the key `myoRepsTimerPresets`. On app load, if this key exists, the stored presets SHALL replace the defaults.

#### Scenario: Presets survive app reload
- **WHEN** the user adds a custom 45s preset and reloads the app
- **THEN** the 45s preset is still visible in the grid

#### Scenario: First load uses defaults
- **WHEN** the localStorage key `myoRepsTimerPresets` does not exist
- **THEN** the default presets (30, 60, 90, 120, 150, 180) are shown

### Requirement: Bilingual timer UI
All Timer tab strings (button labels, input placeholders, state text, control buttons) SHALL use the `t()` translation function with keys in both EN and ES.

#### Scenario: Timer UI in Spanish
- **WHEN** the language is set to ES
- **THEN** all Timer tab text displays in Spanish

#### Scenario: Timer UI in English
- **WHEN** the language is set to EN
- **THEN** all Timer tab text displays in English
