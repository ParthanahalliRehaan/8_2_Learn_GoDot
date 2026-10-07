# Godot / GDScript Notes — Structured Index

Notes are grouped **by topic** (not by day). Every file starts with a small header showing which original file it came from and which plan day it belongs to. Images live in `assets/images/` (byte-for-byte copies, filenames unchanged). The untouched original upload is in `_original_untouched/`.

## Contents

| Folder | What's inside |
|---|---|
| `01_Fundamentals` | Nodes/scenes/signals analogy, project setup, editor checklist, scenes & root nodes, instancing, early FAQ |
| `02_GDScript` | Core language notes, enums, "a `.gd` file is a Resource" |
| `03_Player_Movement` | `CharacterBody2D`, `velocity`/`move_and_slide()`, `input_source`, Input Map, `get_axis`, collision shapes, math |
| `04_Jump_and_Plunge` | Gravity, `enum` state machine, `clamp`, `is_on_floor()` |
| `05_Kick_and_Collision` | `Area2D` hitbox, layers/masks (bitmasks), `area_entered`, `monitoring` |
| `06_Local_Coop_and_Camera` | Two players, code-way instancing, shared `Camera2D`, `lerp`, bug log |
| `07_Enemies_and_Spawning` | `Timer` spawning, enemy enum, nearest-player AI, groups |
| `08_Game_State_Water_Hallucination` | Autoload `GameState`, custom signals, water/health, hallucination, cacti |
| `09_UI_and_Menus` | Start/Pause/Game-Over menus, `CanvasLayer`, anchors, containers, `paused` |
| `10_Progress_Log` | Day 9 recap, Day 12 pointer |
| `assets/images` | All screenshots used by the notes |

## Original folder → plan day → new location

The original folder numbers drifted from the plan's day numbers (e.g. folder `Day4` is plan *Day 2*). This table maps them.

| Original | Plan day | Now in |
|---|---|---|
| `Day0.md` | early doubts | `01_Fundamentals/06` |
| `Day1.md` | setup | `01_Fundamentals/02` |
| `Day2.md` | setup (Day 0) | `01_Fundamentals/01` |
| `Day3/*` | setup | `01_Fundamentals/03, 04` |
| `GDScript.md` | cross-cutting | `02_GDScript/01` |
| `Day4/*` | Day 2 | `03_Player_Movement/*`, `01_Fundamentals/05`, `02_GDScript/03` |
| `Day5/*` | Day 3 | `04_Jump_and_Plunge/01`, `02_GDScript/02` |
| `Day6/*` | Day 4 | `05_Kick_and_Collision/*` |
| `Day7/*` | Day 5 | `06_Local_Coop_and_Camera/*` |
| `Day8/*` | Day 6 | `07_Enemies_and_Spawning/*` |
| `Day9/*` | Day 9 | `10_Progress_Log/01` |
| `Day10/*` | Day 7 | `08_Game_State_Water_Hallucination/*` |
| `Day11/*` | Day 8 | `09_UI_and_Menus/*` |
| `Day12/*` | Day 12 | `10_Progress_Log/02` |

## Quick finder

- **"Why doesn't X work?"** → `06_Local_Coop_and_Camera/03_bug-log-input-source-overwritten.md`, and the "five causes" list in `09_UI_and_Menus/01_lesson-start-pause-gameover-menus.md`
- **Signals** → `05_Kick_and_Collision/04`, `07_Enemies_and_Spawning/02`, `08_Game_State_Water_Hallucination/02`
- **Math** → `03_Player_Movement/05` (vectors), `04_Jump_and_Plunge/01` (gravity/clamp), `05_Kick_and_Collision/02–03` (bitmasks), `06_Local_Coop_and_Camera/02` (lerp), `07_Enemies_and_Spawning/01` (distance_squared_to)
- **Instancing (`preload` → `instantiate` → `add_child`)** → `01_Fundamentals/05`, `06_Local_Coop_and_Camera/02`

## Open questions the notes themselves left unanswered

- Is layer/mask matching one-directional or bidirectional? (`05_Kick_and_Collision/02`)
- `lerp` with a non-reset `t`: constant slide or ease-out? (`09_UI_and_Menus/02`)
- Can multiple `Control` trees share one `CanvasLayer`? Does `Control` need a `CanvasLayer` ancestor? (`09_UI_and_Menus/02`)
- Mid-hallucination Cactus edge case (`08_Game_State_Water_Hallucination/02`)

## What was changed vs. the originals

- Regrouped by topic; `Day4/1_doubts.md` was split at its `# Doubt N` headings.
- Windows line endings (CRLF) normalised to LF.
- Image links rewritten to `../assets/images/...`; one typo fixed (`3_Shp.pmg` → `3_Shp.png`).
- The `./Day3/1_Scene.md` cross-link now points to the new location.
- A source/plan-day header was added to each file. Note text itself was not rewritten.

## Things I noticed but left as-is

- `05_Kick_and_Collision/03` has a garbled line: `hitbox.collision_layer = """But if you want two layers to be switched on its 8 | 4"""` — probably meant `hitbox.collision_layer = 8 | 4`.
- Some files contain leftover chat phrasing (e.g. "That document you just pasted is the stale, wrong version…").
- `01_Fundamentals/01` is titled "Day 0" inside the file but is the Day 2 file in the original numbering.
