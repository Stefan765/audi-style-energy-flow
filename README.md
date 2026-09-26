Audi Style Energy Flow
Custom Home Assistant Lovelace card for visualizing energy flows in a modern Audi-inspired house scene, with dynamic weather/day-night backgrounds and EV-aware layout switching.
Fork notice — This is a community fork of
stexecute/tesla-style-energy-flow
(MIT). The following were contributed by this fork and already merged into upstream:
visual scene-position editor, editor debouncing, vehicle-style label hierarchy,
scene tone gradients per time-of-day, extended background_map keys.
This fork currently adds the following on top of upstream (PR #22 open):

fix: Grid → Battery flow animation now always shown when the
grid charges the battery (with or without simultaneous solar)
(not fully tested — feedback welcome)
feat: Separate battery charge / discharge power entities —
battery_charge_power and battery_discharge_power
feat: Separate grid import / export power entities —
grid_import_power and grid_export_power (SMA SHM 2.0, Victron, Fronius, …)
feat: Smart entity filtering in editor dropdowns (W/kW, %, V, A)
refactor: Fully restructured editor UI — sensors grouped by topic,
visual editor button at the top, friendly names in dropdowns
Upstream maintainer: @stexecute.
Until PR #22 is merged this fork can be installed as a HACS Custom Repository.
Features • Installation • Usage • Troubleshooting • Screenshots • Files • License

Features
Smooth animated SVG energy-flow lines
Flow colors by energy source: solar = yellow, battery = green, grid = red
Dynamic background based on weather, day/night and EV charging state
Audi-inspired visual scene and energy-flow presentation
Scene-specific label and guide positioning for each background
Optional dual-EV support with separate EV1 / EV2 power, battery and charging switch entities
Optional ev_label / ev2_label for custom vehicle names
Optional roof_a_label / roof_b_label for custom PV array names
Optional ev_presence / ev2_presence to show the EV scene when a vehicle is at home, even if it is not charging
Optional scene_path_map and scene_component_map overrides for custom dual-EV backgrounds
Config editor with entity dropdowns
Multilanguage UI (auto, it, en, es, fr, de)
Configurable thresholds for flow visibility:
thresholds.solar_min_w
thresholds.grid_min_w
thresholds.battery_min_w
ev_min_w
Optional ev_hide_when_idle to hide EV labels/guide when not charging
Optional ev_in_load / ev2_in_load for whole-home meters that already include the wallbox draw in load_power (SMA SHM 2.0, SolarEdge total_consumption, …)
The card subtracts EV power from load before flow allocation so the battery is not double-counted
Optional smoothing_seconds (EWMA, default 0) — set to e.g. 10 to dampen cloud-induced jumpiness on Solar, Grid, Battery and Load
EV power remains unsmoothed so charging start/stop transitions remain instant
Optional show_header to show or hide the card title
Optional font_scale to improve readability on compact cards or tablet layouts
Optional battery_invert if your battery sensor uses the opposite sign convention
Battery node hidden automatically when no battery entities are configured
Battery percentage remains readable even when battery power is idle
Simplified node status text with battery-focused charging/discharging state
Installation
HACS (Custom Repository)
HACS → Frontend → Custom repositories
Add this GitHub repository URL:
https://github.com/stexecute/tesla-style-energy-flow
Category: Dashboard
Install Audi Style Energy Flow
Refresh the browser cache
Manual
Copy package files to:
dist/tesla-style-energy-flow.js → /config/www/community/audi-style-energy-flow/audi-style-energy-flow.js
dist/backgrounds/* → /config/www/community/audi-style-energy-flow/backgrounds/*
Add the Lovelace resource:
lovelace:
  resources:
    - url: /local/community/audi-style-energy-flow/audi-style-energy-flow.js
      type: module

Reload the frontend or restart Home Assistant.
Usage
type: custom:audi-style-energy-flow
title: Audi Style Energy Flow
show_header: true
language: auto
background: /local/community/audi-style-energy-flow/backgrounds/scene_day_clear_idle.png
dynamic_background: true
background_asset_base: /local/community/audi-style-energy-flow/backgrounds
battery_invert: false
grid_invert: false
font_scale: 1.0
ev_label: Audi Q8 e-tron
ev2_label: Audi Q4 e-tron
roof_a_label: South
roof_b_label: West
ev_hide_when_idle: false
ev_min_w: 150
thresholds:
  solar_min_w: 50
  grid_min_w: 50
  battery_min_w: 50
entities:
  solar_power: sensor.solar_power
  roof_a_power: sensor.roof_array_a_power
  roof_a_voltage: sensor.roof_array_a_voltage
  roof_a_current: sensor.roof_array_a_current
  roof_b_power: sensor.roof_array_b_power
  roof_b_voltage: sensor.roof_array_b_voltage
  roof_b_current: sensor.roof_array_b_current
  grid_power: sensor.grid_power
  battery_power: sensor.battery_power
  load_power: sensor.home_load_power
  battery_level: sensor.battery_level
  ev_power: sensor.ev_charging_power
  ev_battery: sensor.ev_battery_level
  ev_charge_switch: switch.ev_charge
  ev_presence: binary_sensor.ev_presence

  # Optional second EV — leave empty or omit entirely if you only have one car
  # ev2_power: sensor.your_ev2_charging_power
  # ev2_battery: sensor.your_ev2_battery_level
  # ev2_charge_switch: switch.your_ev2_charge
  # ev2_presence: binary_sensor.your_ev2_presence

  weather: weather.home
  sun: sun.sun

The card ships with built-in SVG flow paths and scene presets, so no extra paths: block is required for a normal installation.
The second EV is optional. If ev2_* entities are not configured, the card behaves exactly like the single-EV version.

If presence entities are configured:

the card can keep the EV scene visible when a car is at home even if charging power is 0
if only one EV is present/active, the single-car scene is reused and mapped to that vehicle
if both EVs are present/active, the dual-EV scene logic is used
Optional roof array sensors can also be added for two array overlays:
roof_a_power
roof_a_voltage
roof_a_current
roof_b_power
roof_b_voltage
roof_b_current
For custom dual-EV scenes you can also override per-scene geometry through:
scene_path_map
scene_component_map
Troubleshooting
The grid → battery line disappears while the car is charging
Symptom: The grid → battery flow line shows correctly while the EV is not
charging, but vanishes as soon as the EV starts charging — even though the grid is
still charging the battery.
Cause: Your load_power sensor is a whole-home / total-consumption meter
that already includes the wallbox draw (common with SolarEdge power_consumption,
SMA SHM 2.0 and other house-level meters).

When ev_in_load is left at its default (false), the EV draw is counted twice in
the flow allocation — once inside the inflated home load and once as the separate EV
path. The card allocates grid import to the home load and the EV before the battery,
so during charging the grid-import budget is exhausted and the computed grid →
battery flow drops to zero, hiding the line.

Fix: Tell the card that the EV is already included in the load meter:

ev_in_load: true
# Set ev2_in_load: true as well if you have a second EV

The card then subtracts the EV power from load_power before allocating flows, so the
grid → battery line stays visible while the car charges.
Only set this if your load meter actually includes the wallbox. If you have a
dedicated EV circuit that is not part of load_power, leave it set to false.

Grid / battery flow direction looks inverted
Some inverters, such as SolarEdge, report the opposite sign from what the card expects.
The card expects:

positive = grid importing
positive = battery charging
If a flow points in the wrong direction, add:
grid_invert: true

and/or:
battery_invert: true

Alternatively, use the separate:
grid_import_power
grid_export_power
battery_charge_power
battery_discharge_power
entities.
Screenshots
Day clear — idle
Day rain — EV charging
Night clear — EV charging
Night rain — idle
Night rain — grid + home + EV
Files
dist/tesla-style-energy-flow.js: packaged card file used by HACS
dist/backgrounds/: packaged background assets used by HACS
hacs.json: HACS metadata
examples/lovelace-card.yaml: configuration example
docs/screenshots/: preview images for README
License
MIT
Trademark notice
This project is an independent, community-built Home Assistant Lovelace card. It is not affiliated with, endorsed by, sponsored by, or officially connected to Audi AG or any of its subsidiaries.
"Audi" and related marks are trademarks of Audi AG, used here only to describe the visual inspiration or theme of this community project. The card does not redistribute Audi artwork, logos, screenshots or other Audi-owned material. All graphics shipped with the project are original work intended to create a premium automotive-inspired visual aesthetic.

If you are an Audi representative and have concerns about this project, please open an issue and the maintainers will respond promptly.
