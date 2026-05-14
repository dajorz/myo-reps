## MODIFIED Requirements

### Requirement: Tab bar displays available tabs
The app SHALL render a horizontal tab bar at the top of the screen with two tabs: "Myo-Reps" and "Timer". The active tab SHALL be visually highlighted following Material Design 3 Secondary Tabs visual style. Tab labels SHALL use translated strings via `t()`.

The tab bar SHALL conform to the following M3 Secondary Tabs visual tokens (dark theme):
- Container height: 48dp
- Active label color: on-surface (#ffffff)
- Inactive label color: on-surface-variant (gba(255,255,255,0.60))
- Active indicator: Primary color (#3b82f6), 2dp height, full tab width, corner radius 3px 3px 0 0
- Divider: outline-variant (gba(255,255,255,0.12)), 1dp, at bottom of container
- Container background: Surface (transparent, inherits app background)
- Label typography: Title Small — 14sp, font-weight 500
- Hover state layer: on-surface 8% (gba(255,255,255,0.08)) background, no vertical transform
- Press state layer: on-surface 12% (gba(255,255,255,0.12)) background

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
