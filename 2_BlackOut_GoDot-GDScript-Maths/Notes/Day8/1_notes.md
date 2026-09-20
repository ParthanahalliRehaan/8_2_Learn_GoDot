## 🔤 GDScript — functions & concepts

**`preload(path) -> Resource`**
Resolves and loads a resource **at compile time** (when the script parses). `var enemy_scene: PackedScene = preload("res://scenes/enemy/Enemy.tscn")` — cached instantly, no runtime cost per use.

**`.instantiate() -> Node`**
Called on a loaded `PackedScene`. Builds a live `Node` (and its children) from the scene blueprint, in memory — not yet part of the running game.

**`add_child(node: Node)`**
Attaches a node as a child of whoever calls it. This is the exact moment `_ready()` fires on the new node and its children — which is why `enemy.enemy_type = randi_range(0, 1)` has to happen **before** this call, not after.

**`enum` + `match`**
`enum EnemyType { RATTLESNAKE, SAHARAN_VIPER }` — a closed set of named integer constants, referred to by name not number. `match enemy_type:` with per-value branches is GDScript's switch-statement equivalent.

**Groups — `add_to_group()` / `get_nodes_in_group()`**
Not a keyword or type — an arbitrary string tag. `add_to_group("players")` (must be inside `_ready()`, on the Player script) tags a node; `get_tree().get_nodes_in_group("players")` returns every node currently carrying that exact string. Zero validation between the two calls — a mismatched string just silently returns an empty array.

**`abs(value)`**
Global function, not a method — `abs(x)`, not `x.abs()`. Strips sign: `abs(-7) == 7`. Used because position subtraction gives a *signed* difference; "how far apart" only cares about magnitude.

**`randi_range(a, b)` / `randf_range(a, b)`**
Random integer / random float, inclusive range. Used for `enemy_type` selection and spawn position variance.

**`queue_free()`**
Doesn't delete instantly — schedules the node for removal from the tree and memory cleanup at the **end of the current frame**, once it's safe. Once freed: stops rendering, stops running `_physics_process()`, stops colliding, gone from the tree entirely.

**Dictionaries as input schemes**
`player1.input_source = { "left": "p1_left", "right": "p1_right", ... }` — key→value pairs, keys are your internal action names, values are the actual Input Map action strings. This is the Day 2 `input_source` pattern applied per-instance in `main.gd`, letting one shared `player.gd` script drive two differently-controlled Players.

**`@onready`**
`@onready var shared_camera: Camera2D = $SharedCamera` — delays this assignment until the node tree (including `SharedCamera`) actually exists, instead of running at script-parse time before the tree is built. Needed anywhere you reference a sibling/child node via `$NodePath`.

**`lerp(from, to, weight)`**
Linearly interpolates between two values. `lerp(min_zoom, max_zoom_out, t)` — when `t = 0`, result is `min_zoom`; when `t = 1`, result is `max_zoom_out`; anywhere between, it's a proportional blend. Here, `t` itself comes from `clamp(distance / max_distance, 0.0, 1.0)` — distance turned into a 0–1 "how close to max" ratio, which then drives how far along the `min_zoom`→`max_zoom_out` range the camera's zoom should sit.

**`clamp(value, min, max)`**
Forces a value to stay within a range — anything below `min` becomes `min`, above `max` becomes `max`. Used here to guarantee `t` never exceeds `1.0` even if two players somehow get further apart than `max_distance`.

## 🎮 Godot (engine & editor concepts)

**Timer node**
`wait_time`, `autostart`, `one_shot` (false = repeats), emits `timeout` signal on each countdown. Connected via Node dock → Signals tab, same mechanism as collision signals.

**`_ready()` timing**
Fires the instant a node enters the tree, *during* `add_child()` — before that calling line even finishes. Root cause of every "set this before/after add_child" ordering question this whole project.

**`is_on_floor()`**
`CharacterBody2D` built-in — true when resting against floor-facing collision. Gates gravity accumulation to avoid jitter/sinking once grounded.

**Collision Layers vs Masks**
Layer = what a body *is*. Mask = what a body *looks for*. Give `Enemy` a distinct Layer; make sure Player's `Hitbox` Mask includes it, or Day 4's kick detection silently never fires against enemies.

**Why no `Spawner` node**
`main.gd` already owns Player and Enemy instancing directly — a separate `Spawner` node/script would duplicate logic for no gain. `spawner.gd` is dead code; delete it once confirmed unreferenced.

**GDScript top-level restriction (the bug above)**
A `.gd` file's top level can only contain declarations — `extends`, `var`, `const`, `func`, `signal`, `enum`, `class`. Executable statements (like `add_to_group(...)`) must live inside a function body. This is why that line needs to move into `_ready()`.

## Signal
### 🎮 Timer signal connection (the piece I skipped)

In your actual build, the `Timer` node was added as a **child of `Main`** (not a separate `Spawner`), so its `timeout` signal connects directly to `main.gd`.

**How that connection was made, mechanically:**
1. Select the `Timer` node in the Scene dock.
2. Node dock → **Signals** tab lists every signal that node type can emit — `Timer` shows `timeout()` among them.
3. Double-clicking `timeout()` opens a "Connect a Signal" dialog. The **target node** you pick there determines which script's `_on_...()` function gets called — you picked `Main`, so the generated function lives in `main.gd`, not in a `Timer`-attached script (Timer nodes don't take their own custom scripts in this setup, they just emit signals for something else to listen to).
4. Godot auto-generates the function name based on the *signal name* + the *emitting node's name* — since your node is literally named `Timer`, you got `_on_timer_timeout()`. If you'd renamed the node to `SpawnTimer` first, you'd have gotten `_on_spawn_timer_timeout()` instead — the function name isn't arbitrary, it's derived from whatever the node is called at the moment you connect.
5. This connection is stored **in the `.tscn` scene file itself**, not in the script — that's why you can see it reflected as a small icon/indicator next to `timeout()` in the Signals tab once connected, and why deleting the `Timer` node later would also silently break the connection (the function would remain in `main.gd`, just never called again).

**One thing worth checking, since you have both `enemy.gd`'s internal `idle_timer` (a plain `float` incremented by `delta`, not a `Timer` node) and this actual `Timer` node driving spawns** — do you know which of the two is a real Godot node with a signal, and which is just a variable you're manually counting up yourself? They solve similar-sounding problems ("track time passing") in genuinely different ways — one is engine-managed and signal-driven, the other is code you're rolling by hand inside `_physics_process()`. Worth being able to explain that distinction out loud if asked.