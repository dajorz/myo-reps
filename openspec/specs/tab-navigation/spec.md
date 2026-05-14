## Requirements

### Requirement: Tab bar displays available tabs
The app SHALL render a horizontal tab bar at the top of the screen with two tabs: "Myo-Reps" and "Timer". The active tab SHALL be visually highlighted following Material Design 3 Secondary Tabs visual style. Tab labels SHALL use translated strings via `t()`.

The tab bar SHALL conform to the following M3 Secondary Tabs visual tokens (dark theme):
- Container height: 48dp
- Active label color: on-surface (#ffffff)
- Inactive label color: on-surface-variant (
gba(255,255,255,0.60))
- Active indicator: Primary color (#3b82f6), 2dp height, full tab width, corner radius 3px 3px 0 0
- Divider: outline-variant (
gba(255,255,255,0.12)), 1dp, at bottom of container
- Container background: Surface (transparent, inherits app background)
- Label typography: Title Small — 14sp, font-weight 500
- Hover state layer: on-surface 8% (
gba(255,255,255,0.08)) background, no vertical transform
- Press state layer: on-surface 12% (
gba(255,255,255,0.12)) background

#### Scenario: App loads with Myo-Reps tab active
- **WHEN** the user opens the app
- **THEN** the tab bar is visible with "Myo-Reps" and "Timer" tabs
- **AND** the "Myo-Reps" tab is active by default
- **AND** the Myo-Reps content is visible

#### Scenario: Active tab shows M3 indicator
- **WHEN** a tab is active
- **THEN** the tab label is displayed in on-surface color (white)
- **AND** a 2dp full-width underline indicator in Primary color is shown at the bottom of the tab
- **AND** the indicator has corner radius 3px on top corners and 0px on bottom corners
- **AND** no background fill is applied to the active tab container

#### Scenario: Inactive tab appearance
- **WHEN** a tab is inactive
- **THEN** the tab label is displayed in on-surface-variant color (rgba(255,255,255,0.60))
- **AND** no underline indicator is shown

#### Scenario: Hover state layer
- **WHEN** the user hovers over a tab
- **THEN** a subtle background (rgba(255,255,255,0.08)) appears
- **AND** the tab does not move vertically

#### Scenario: Tab bar divider
- **WHEN** the tab bar is rendered
- **THEN** a 1dp divider line in outline-variant color (rgba(255,255,255,0.12)) separates the tab bar from the content below

#### Scenario: Tab labels update on language change
- **WHEN** the user switches language from EN to ES
- **THEN** the tab labels update to their Spanish translations

### Requirement: Tapping a tab switches content
The app SHALL show the corresponding tab content and hide the other when a tab is tapped. The tab bar highlight SHALL update to reflect the active tab.

#### Scenario: Switch from Myo-Reps to Timer
- **WHEN** the user taps the "Timer" tab
- **THEN** the Myo-Reps content is hidden
- **AND** the Timer content is shown
- **AND** the "Timer" tab is highlighted as active

#### Scenario: Switch back to Myo-Reps
- **WHEN** the user is on the Timer tab and taps "Myo-Reps"
- **THEN** the Timer content is hidden
- **AND** the Myo-Reps content is shown with its state preserved

### Requirement: Swipe gesture switches tabs
The app SHALL detect horizontal swipe gestures on the screen to switch between tabs. A swipe-left SHALL navigate to the next tab (right). A swipe-right SHALL navigate to the previous tab (left). The swipe SHALL require a minimum horizontal distance of 50px and a horizontal-to-vertical ratio greater than 1.5 to avoid conflicts with vertical scrolling.

#### Scenario: Swipe left to Timer tab
- **WHEN** the user is on the Myo-Reps tab
- **AND** performs a horizontal swipe-left (> 50px, ratio > 1.5)
- **THEN** the app switches to the Timer tab

#### Scenario: Swipe right to Myo-Reps tab
- **WHEN** the user is on the Timer tab
- **AND** performs a horizontal swipe-right (> 50px, ratio > 1.5)
- **THEN** the app switches to the Myo-Reps tab

#### Scenario: Vertical scroll does not trigger tab switch
- **WHEN** the user performs a primarily vertical swipe (ratio < 1.5)
- **THEN** no tab switch occurs

### Requirement: Active tab persists across sessions
The last active tab SHALL be saved to localStorage and restored when the app reloads.

#### Scenario: Restore last active tab
- **WHEN** the user was on the Timer tab and reloads the app
- **THEN** the Timer tab is active on reload
