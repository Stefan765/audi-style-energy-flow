Audi Style Energy Flow
A custom Home Assistant Lovelace card for visualizing home energy flows in a modern, automotive-inspired scene.
The card provides animated energy flows between solar, grid, battery, home consumption and electric vehicles, combined with dynamic weather, day/night and EV-aware backgrounds.

Community fork
This project started as a community fork of
stexecute/tesla-style-energy-flow
and is licensed under the MIT License.

The project has been redesigned and renamed as Audi Style Energy Flow, with an independent visual identity and custom background artwork.
Features
Smooth animated SVG energy-flow lines
Dynamic energy-flow visualization for:
☀️ Solar
🔋 Battery
⚡ Grid
🏠 Home consumption
🚗 Electric vehicles
Dynamic backgrounds based on:
Weather
Day/night
EV charging state
EV presence
Automotive-inspired visual design
Scene-specific label and guide positioning
Optional dual-EV support
Separate EV1 / EV2 power, battery and charging entities
Optional custom vehicle names
Optional custom PV array names
Optional EV presence detection
Automatic single-EV / dual-EV scene switching
Configurable energy-flow thresholds
Smart entity filtering in the visual editor
Fully restructured configuration editor
Friendly entity names in dropdowns
Multilingual UI:
English
German
French
Spanish
Italian
Automatic language detection
Optional smoothing for unstable sensor values
Battery charge/discharge state visualization
Automatic battery-node hiding when no battery is configured
Responsive layout for dashboards, tablets and wall-mounted displays
Energy Flow
The card visualizes energy movement between the main components of your home energy system.
                 ☀️ SOLAR
                    │
                    ▼
              ┌───────────┐
              │    HOME   │
              └───────────┘
                 │     │
          ┌──────┘     └──────┐
          ▼                   ▼
       🔋 BATTERY           🚗 EV
          │
          ▼
        ⚡ GRID

The actual flow paths and directions are calculated dynamically from the configured sensor values.
Installation
HACS
The recommended installation method is HACS.
Open HACS in Home Assistant.
Go to Frontend.
Open the three-dot menu.
Select Custom repositories.
Add your GitHub repository:
https://github.com/YOUR_USERNAME/audi-style-energy-flow

Select Dashboard as the category.
Click Add.
Search for Audi Style Energy Flow.
Install the card.
Restart Home Assistant or reload the frontend.
Replace YOUR_USERNAME with the GitHub account or organization that owns this repository.
Manual Installation
Copy the packaged card and background assets to:
/config/www/community/audi-style-energy-flow/

The resulting structure should look like:
/config/www/community/audi-style-energy-flow/
├── audi-style-energy-flow.js
└── backgrounds/
    ├── scene_day_clear_idle.png
    ├── scene_day_rain_idle.png
    ├── scene_day_clear_ev.png
    └── ...

Then add the Lovelace resource:
lovelace:
  resources:
    - url: /local/community/audi-style-energy-flow/audi-style-energy-flow.js
      type: module

After installation, reload the Home Assistant frontend.
Usage
Basic configuration:
type: custom:audi-style-energy-flow
title: Audi Style Energy Flow
show_header: true

language: auto

background: /local/community/audi-style-energy-flow/backgrounds/scene_day_clear_idle.png

dynamic_background: true

background_asset_base: /local/community/audi-style-energy-flow/backgrounds

font_scale: 1.0

battery_invert: false
grid_invert: false

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
  battery_level: sensor.battery_level

  load_power: sensor.home_load_power

  ev_power: sensor.ev_charging_power
  ev_battery: sensor.ev_battery_level
  ev_charge_switch: switch.ev_charge
  ev_presence: binary_sensor.ev_presence

  # Optional second EV
  # ev2_power: sensor.ev2_charging_power
  # ev2_battery: sensor.ev2_battery_level
  # ev2_charge_switch: switch.ev2_charge
  # ev2_presence: binary_sensor.ev2_presence

  weather: weather.home
  sun: sun.sun

Dual EV Support
A second EV is optional.
If no ev2_* entities are configured, the card automatically behaves as a single-EV configuration.

When presence entities are configured, the card can automatically switch between different vehicle scenes.

One vehicle present
The single-EV scene is displayed and mapped to the active vehicle.
Two vehicles present
The dual-EV scene is activated.
The following entities can be configured for the second vehicle:

entities:
  ev2_power: sensor.ev2_charging_power
  ev2_battery: sensor.ev2_battery_level
  ev2_charge_switch: switch.ev2_charge
  ev2_presence: binary_sensor.ev2_presence

Solar / PV Arrays
Two separate PV arrays can optionally be displayed.
entities:
  roof_a_power: sensor.roof_array_a_power
  roof_a_voltage: sensor.roof_array_a_voltage
  roof_a_current: sensor.roof_array_a_current

  roof_b_power: sensor.roof_array_b_power
  roof_b_voltage: sensor.roof_array_b_voltage
  roof_b_current: sensor.roof_array_b_current

Custom labels can be configured:
roof_a_label: South
roof_b_label: West

Grid and Battery Sensors
The card supports separate import/export and charge/discharge sensors.
Grid
entities:
  grid_import_power: sensor.grid_import_power
  grid_export_power: sensor.grid_export_power

This is useful with systems such as:
SMA
Victron
Fronius
SolarEdge
Other energy-management systems
Battery
Separate battery charge/discharge sensors are also supported:
entities:
  battery_charge_power: sensor.battery_charge_power
  battery_discharge_power: sensor.battery_discharge_power

Using separate sensors is recommended when the inverter provides them.
Flow Thresholds
Energy flows can be hidden below configurable thresholds.
thresholds:
  solar_min_w: 50
  grid_min_w: 50
  battery_min_w: 50

The EV threshold can be configured separately:
ev_min_w: 150

This helps prevent small sensor fluctuations from creating unnecessary animated flows.
Sensor Smoothing
Solar and household energy sensors can sometimes fluctuate rapidly, especially when clouds pass over a PV system.
Optional smoothing can be enabled:

smoothing_seconds: 10

The default is:
smoothing_seconds: 0

EV charging power is intentionally not smoothed so that charging start/stop transitions remain immediate.
EV Power Included in Home Load
Some whole-home energy meters already include the EV wallbox in their load_power value.
Examples include:

SMA SHM 2.0
SolarEdge total consumption
Other whole-house consumption meters
In this situation, enable:
ev_in_load: true

For a second EV:
ev2_in_load: true

The card will subtract the EV power from the home load before calculating the individual energy flows.
Example
Without ev_in_load:
Grid → Home Load
Grid → EV
Grid → Battery

The EV power may effectively be counted twice.
With:

ev_in_load: true

the card correctly separates the EV consumption from the remaining household load.
Only enable this option when your load_power sensor already includes the wallbox consumption.
Troubleshooting
Grid → Battery flow disappears while EV is charging
If the grid → battery flow disappears when the EV starts charging, check whether your load_power sensor already includes the wallbox.
If it does, enable:

ev_in_load: true

For two EVs:
ev_in_load: true
ev2_in_load: true

Grid or Battery Flow Direction Is Inverted
The card normally expects:
Grid:
positive = importing from grid

Battery:
positive = charging

If your inverter uses the opposite sign convention, use:
grid_invert: true

and/or:
battery_invert: true

Alternatively, use dedicated import/export or charge/discharge entities.
Custom Scene Geometry
Advanced users can customize scene geometry using:
scene_path_map:

and:
scene_component_map:

This allows custom positioning of flow paths, labels and scene components for alternative backgrounds.
Backgrounds
The card supports dynamic background selection.
Example:

dynamic_background: true

background_asset_base: /local/community/audi-style-energy-flow/backgrounds

Background selection can take into account:
Day
Night
Clear weather
Rain
EV charging
EV presence
Single EV
Dual EV
Idle state
All distributed background graphics are original project assets.
Project Structure
audi-style-energy-flow/
├── dist/
│   ├── audi-style-energy-flow.js
│   └── backgrounds/
│       ├── scene_day_clear_idle.png
│       ├── scene_day_clear_ev.png
│       ├── scene_day_rain_idle.png
│       └── ...
│
├── docs/
│   └── screenshots/
│       ├── 01-day-clear-idle.png
│       ├── 02-day-rain-charging.png
│       ├── 03-night-clear-charging.png
│       ├── 04-night-rain-idle.png
│       └── 05-night-rain-grid-home-ev.png
│
├── examples/
│   └── lovelace-card.yaml
│
├── hacs.json
├── README.md
└── package.json

Screenshots
Day — Clear / Idle
Day — Rain / EV Charging
Night — Clear / EV Charging
Night — Rain / Idle
Night — Rain / Grid + Home + EV
Files
File / Directory	Description
dist/audi-style-energy-flow.js	Packaged Lovelace card
dist/backgrounds/	Background assets
hacs.json	HACS metadata
examples/lovelace-card.yaml	Example configuration
docs/screenshots/	README screenshots
README.md	Project documentation

Development
Clone the repository:
git clone https://github.com/YOUR_USERNAME/audi-style-energy-flow.git
cd audi-style-energy-flow

Install dependencies:
npm install

Build the project:
npm run build

The resulting card should be generated as:
dist/audi-style-energy-flow.js

License
MIT
Trademark Notice
Audi Style Energy Flow is an independent, community-built Home Assistant project.
This project is not affiliated with, endorsed by, sponsored by, or officially connected to Audi AG or any of its subsidiaries.

"Audi", the Audi name, logos, vehicle names and other related marks are trademarks of Audi AG. They are referenced only where necessary to describe compatibility, theme or visual inspiration.

This project does not redistribute official Audi software, logos, vehicle renders, screenshots or other proprietary Audi assets.

All custom graphics and backgrounds distributed with this project are original project assets.

If you are a trademark owner and have concerns regarding this project, please open an issue in the repository.
