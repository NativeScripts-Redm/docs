# ns-christmas (RedM)

Decorate any part of the frontier with Christmas props and coloured lights. Admins place props in game and every player sees them.

## Features

- 135 Native Scripts props: snowmen, Santas, reindeer, sleighs, nutcrackers, gingerbread, ten coloured presents, market huts, snow patches and 70 light props (arches, a walk-through tunnel, a canopy, icicles, light trees, outlines, numbers 0-9 and a Happy New Year sign)
- Light text: type your own words and they are placed in bulb letters (A-Z, Turkish letters, numbers and ! ? . , - '), as one group
- 55 of the game's own props in a searchable catalogue with favourites and recently placed: Christmas trees, snowy firs, wreaths, bows, gift boxes, giant candy canes and gingerbread, pies and candy jars, paper lanterns, lamp posts, candles, fire basins, vendor carts, tents, benches and snowy crates, logs, fences and wagons
- Light props: pick one of 11 colours, steady, blinking or rainbow, changeable after placing
- Snow across the whole map with one switch, with snow on the ground
- Fireworks rocket and fireworks box: any player can light a placed one with G; after a 5 second fuse a show goes up that only that player sees (30 second cooldown)
- Four ready-made layouts (Town Tree, Lantern Walk, Christmas Market, New Year Fireworks) and your own: save an area, then place it anywhere
- Six town scenes: Valentine, Saint Denis, Blackwater, Strawberry, Rhodes and Annesburg. Go takes you there; Place lays the scene out on its spot
- Every layout shows up as a preview first, so you slide or turn the whole scene before it is saved
- Place a row: up to 20 copies of a prop in a line, with the gap you choose; the row follows the slope of the ground
- Select several placed props and move or delete them together
- Undo for every edit (last 20 per admin)
- Season dates: decorations and snow show only between two dates you set, and admins can still decorate early
- Saved in SQL, restored on restart
- Props are local objects on each client, so they do not use networked entity slots
- Works with VORP, RSG-Core and RedEM:RP through ns-lib

## Requirements

New custom props are streamed: after installing or updating, restart the whole server and reconnect (a resource `restart` does not load new stream files).

Do not `restart ns-christmas` while players are online. RedM can crash a client that has seen the custom props once it moves after the restart. Restart the whole server instead.

### Needs

- `ns-lib` 1.5.0 or newer
- oxmysql or mysql-async

## Install

1. Put `ns-christmas` in your resources folder.
2. Add `ensure ns-christmas` to your server config, after `ns-lib`.
3. Add `add_ace group.admin ns-christmas.admin allow`, or rely on `Config.AdminGroup`.

The tables `ns_christmas_props` and `ns_christmas_layouts` are created on first start.

## Use

Type `/xmas`. Pick a prop and press Place. You can walk while the menu is open; typing in a text box stops the walking.

### Placing keys

| Key | What it does |
|---|---|
| Aim | The prop follows the point you look at |
| G | Stop or start following your aim |
| Mouse wheel | Turn by `Config.RotationStep` degrees (Shift: 1 degree) |
| Arrow keys | Slide the prop, relative to the camera (Shift: fine steps) |
| Page Up / Page Down | Raise / lower |
| Alt | Drop to the ground |
| Shift + Alt | Put its back against the wall behind it |
| Enter | Save |
| Backspace / Esc | Cancel |

Moving a placed prop starts in place (not following your aim); press G to pick it up with your aim.

The Placed tab lists what is near you, nearest first, with a search box. Click a thumbnail (or Shift-click a row) to select several props, then Move together or Delete them. The undo button in the header (or Ctrl+Z) takes back your last edit. Remove all (World tab) cannot be undone.

The Layouts tab lists Places and Sets. A set appears ahead of you as a preview: slide or turn it, then press Enter to save it or Backspace to drop it. A place opens on its own spot: press its Go button first to stand there. The town scenes sit next to each town's stagecoach stop; check each one in the preview, since a building or a road may need the set moved or turned. Save area stores every prop within 30 m of you as a new layout.

Set `Config.ShowFrom` and `Config.ShowUntil` (for example `'12-01'` and `'01-07'`, server clock) to show decorations and snow only in that season.

The World tab switches snow across the map for every player. The setting survives a restart. While snow is on, `weathersync` (VORP) and `rsg-weathersync` are paused on each client; when snow goes off they get their weather back. Any other weather script is overridden every 2 seconds.

## Config

| Setting | Default | What it does |
|---|---|---|
| `Command` | `xmas` | Opens the editor |
| `AdminAce` / `AdminGroup` | `ns-christmas.admin` / `admin` | Who may edit |
| `MaxProps` | 3000 | Decorations on the whole server |
| `MaxLayoutItems` | 150 | Props in one layout, selection or row |
| `MaxRow` | 20 | Props in one "place a row" |
| `MaxTextLetters` | 32 | Characters in one light text |
| `UndoSteps` | 20 | Edits each admin can take back |
| `ShowFrom` / `ShowUntil` | nil | Season dates, `'MM-DD'` |
| `RotationStep` | 15 | Mouse wheel turn step in degrees |
| `MoveStep` | 0.05 | Metres per arrow / Page Up / Page Down step |
| `AimDistance` | 25 | How far the prop follows your aim |
| `StreamDistance` | 180 | Metres; further decorations are not spawned |
| `MaxVisible` | 400 | Nearest decorations kept spawned |
| `MaxDynamicLights` | 6 | Real light sources per player, 0 = off |
| `LightsNightOnly` | true | Real light sources only at night |
| `BlinkMs` / `RainbowMs` | 700 / 1500 | Blink and rainbow speed |
| `SnowWeather` | `SNOW` | Weather used for map-wide snow |
| `SnowCoverage` | 3 | Ground snow type while snow is on: 0 map default, 1 secondary, 2 Christmas, 3 Christmas secondary |
| `FireworkDelay` / `FireworkCooldown` | 5 / 30 | Fuse and cooldown in seconds |

Messages are in `Config.Messages`.

## Adding props

Add a row to `shared/catalog.lua`: `model`, `label`, `category`, and `collision = true` for solid props. `light = true` gives the prop a coloured light source (`glowZ` = height of the light above the prop's origin, `glowRange` = reach). A model the game build does not have is greyed out in the menu.
