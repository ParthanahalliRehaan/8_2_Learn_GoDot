# Day 6: Snakes — Spawning & AI Targeting Multiple Players
(Day 5 done, i.e. Local Co-op — two Ostriches, one screen, shared camera)

## Revision history (read this first)

1. **Original draft:** two separate scenes (`RattleSnake.tscn` / `SaharanViper.tscn`).
   **Corrected to:** a single `Enemy.tscn` + an `EnemyType` enum — both snakes share identical behavior/collision, only stats/color differ at this stage.
2. **Original draft:** a dedicated `Spawner` node (`Node2D`) + separate `spawner.gd` script, as a sibling of Player(s).
   **Actual build:** `main.gd` already owned direct instancing of `player_scene`/`enemy_scene` (written before this plan was finalized), so a `Timer` was added directly as a child of `Main` instead, with the handler function living inside `main.gd`. **No `Spawner` node was ever created — `spawner.gd` is unused and should be deleted.** This is a legitimate architecture, not a mistake, given `main.gd`'s existing responsibilities — see the note at the very end on when to reconsider splitting it out.
3. **Added mid-build, not in the original scope:** gravity (`velocity.y += gravity * delta`, gated by `is_on_floor()`), and a `stop_distance` check so an enemy stops closing horizontal distance once within a tunable threshold of its target, instead of overlapping/jittering on top of the player.

By the end of today you'll be able to:
- Explain the difference between a scene *definition* (`.tscn` on disk) and a scene *instance* (a live node in the running tree), and load one at runtime with `preload` + `instantiate()`
- Use a `Timer` node and its `timeout` signal to spawn enemies on an interval instead of all at once
- Write `enemy.gd` so a single scene can represent multiple enemy "types" via an `enum`, instead of duplicating scenes
- Write `enemy.gd` so a snake picks whichever co-op player is *nearest*, instead of always targeting Player 1
- Understand direction vectors and `.normalized()` well enough to explain *why* an un-normalized direction would make your snake move at the wrong speed
- Understand why `distance_squared_to()` is preferred over `distance_to()` for comparison-only checks
- Understand *why* node ordering matters: setting a property before vs. after `add_child()`, given `_ready()` fires during `add_child()`
- Explain why gravity needs an `is_on_floor()` guard, and why a self-correcting `if/else` (not an early `return`) is the right shape for a "stop within X px" check that must resume every frame

## 🔤 GDScript (the language itself)

**Preloading vs loading**
- `const EnemyScene := preload("res://scenes/enemies/Enemy.tscn")` (or, in your actual `main.gd`, `var enemy_scene: PackedScene = preload(...)`) — `preload` resolves the path and loads the resource **at compile time**, so it's cached and ready instantly.
- `load(path)` — resolved **at runtime**, only needed when the path itself must be computed dynamically. Not needed here.

**Instancing at runtime**
- `var enemy = enemy_scene.instantiate()` — turns the scene resource (blueprint) into an actual live `Node`. Not part of the running game yet.
- `add_child(enemy)` — attaches it as a child of whichever node calls this (in your build: `Main`). This is the exact moment `_ready()` fires on the new node.
- Any property that a `_ready()`-time check depends on (like `enemy_type`) must be set **before** `add_child()`.

**Enums**
- `enum EnemyType { RATTLESNAKE, SAHARAN_VIPER }` — a closed set of named integer constants. Refer to them by name (`EnemyType.RATTLESNAKE`), never by their underlying number.
- `@export var enemy_type: EnemyType = EnemyType.RATTLESNAKE` — gives you a dropdown in the Inspector instead of a free-typed field.

**`match` statements**
`match enemy_type:` with branches per enum value — GDScript's switch-statement equivalent, cleaner than chained `if/elif` for one variable with several possible values.

**Randomness**
- `randi_range(a, b)` — random integer, inclusive both ends. Used for `enemy_type` selection.
- `randf_range(a, b)` — random float in a range. Used for spawn x-position.

**Groups — a pure tagging system**
`add_to_group("players")` is not a keyword or type — it's an arbitrary string label you invent, stuck onto a node. `get_tree().get_nodes_in_group("players")` asks the scene tree for every node currently carrying that exact string. Godot does zero validation between the two calls — a mismatched string (`"Player"` vs `"players"`) silently returns an empty array, no error. Both strings must match character-for-character.

**`abs()`**
Built-in global function — `abs(x)`, not a method on the value. Strips the sign: `abs(-7) == 7`. Needed because `target.global_position.x - global_position.x` is signed (negative if target is left of enemy), but "how far apart" should only care about magnitude.

**`if/else` vs `if ... return` for a resumable check**
`_physics_process(delta)` runs fresh, every frame, unconditionally. An `if x_distance >= stop_distance:` / `else:` block re-evaluates every single frame automatically — no permanent flag needed, so it naturally resumes chasing the moment distance grows again. An early `return` here would also skip `move_and_slide()` for that frame, silently breaking gravity too — that's why the gravity call and `move_and_slide()` stay unconditional, outside the distance check.

## 🎮 Godot (engine & editor concepts)

**Timer node**
`wait_time`, `autostart`, `one_shot` (false = repeats). Emits `timeout` on each countdown completion. Same signal-connection pattern as `body_entered`/`area_entered` from Day 4.

**`_ready()` timing relative to `add_child()`**
`_ready()` fires the instant a node enters the tree — *during* `add_child()`, before that line of calling code even finishes. This is why `enemy_type` must be set before, not after.

**`is_on_floor()`**
`CharacterBody2D` built-in — true when the collision shape currently rests against floor-facing collision. Gates gravity accumulation so it doesn't keep adding downward force (causing jitter/sinking) once already grounded.

**Collision Layers vs Masks — needed once Enemy exists alongside Player's Hitbox**
- **Layer** = what a body/area *is* (which category it identifies as).
- **Mask** = what a body/area *looks for* (which categories it detects overlap with).
Give `Enemy`'s root a distinct **Layer** (not reused from Player's own body layer). Then on `Player`'s `Hitbox` (`Area2D`, from Day 4), make sure its **Mask** includes that Enemy layer — otherwise your Day 4 kick detection will never fire against enemies, since it was only ever configured against whatever placeholder existed before Enemy was built.

**Why no `Spawner` node was created**
A dedicated `Spawner` only earns its place if it's doing something `Main` isn't already doing. Since `main.gd` already preloads `enemy_scene` and owns Player instancing, adding a separate `Spawner` node would duplicate logic across two files for no functional gain. The `Timer` was added directly as a child of `Main`, and its handler lives in `main.gd`. `spawner.gd` (the file) is orphaned — nothing references it — and should be deleted via the FileSystem dock, after confirming with **Find in Files** that no scene or script preloads/extends it.

## 📐 Math (this day's core)

**Direction vectors toward a moving target**
`direction = (target.global_position - enemy.global_position).normalized()` — subtraction gives a vector pointing from enemy to target; normalizing strips its length to 1 so multiplying by `speed` gives consistent movement speed regardless of distance.

**`distance_squared_to()` vs `distance_to()`**
`distance_to()` computes a square root internally — real cost when checked every physics frame, for every enemy. Squared distances preserve the same ordering as real distances for comparison purposes (`a < b` ⇔ `a² < b²`, for non-negative values), so `distance_squared_to()` gives a correct nearest-target comparison without paying for the square root. Reserve `distance_to()` for when you need the *actual* distance value.

**Nearest-target selection as a min-search loop**
Assume the first player found is "best," track its squared distance; loop the rest, replace "best" whenever a smaller squared distance turns up. Same shape as finding the smallest number in any list.

**Gravity as constant downward acceleration**
`velocity.y += gravity * delta`, gated by `not is_on_floor()`. Same acceleration/delta-time idea introduced for Player in Day 3, reused here for Enemy.

---

## 🤔 Doubts you should be asking yourself

**On the single-scene + enum design**
- Can you set `enemy.enemy_type` before the node is even in the tree? Why does that work fine, while `global_position` behaves differently pre-tree?
- If you forget to set `enemy_type` before `add_child()`, what value silently gets used instead?

**On instancing and ordering**
- What happens if you call `.instantiate()` but forget `add_child()` — does the game error, or does the enemy silently not exist?
- Does setting `global_position` before `add_child()` behave the same as setting `enemy_type` before it? Test this specifically rather than assuming it generalizes.

**On the Timer and where it lives**
- If `wait_time` is 2.0 and `one_shot` is false, does the first enemy spawn instantly at time 0, or only after the first 2 seconds?
- Given `main.gd` already owns Player and Enemy instancing, does it make architectural sense for the Timer's handler to live there too, or would you make a different call once Day 7 (water/game state) and Day 8 (UI/win-lose) start adding to `main.gd`'s workload?

**On targeting multiple players**
- If there's only one player on screen (singleplayer), does your nearest-target loop still work, or does it silently assume exactly two players exist?
- What happens to the comparison loop if `get_nodes_in_group("players")` comes back empty?

**On gravity and the stop-distance check**
- Why does gravity need to be unconditional (running even when `target == null`), while horizontal chase logic doesn't?
- Walk through your `if x_distance >= stop_distance:` / `else:` with actual shrinking-then-growing distance values (say 8 → 3 → 9) — does it correctly resume chasing once distance grows again, or does something get stuck?
- If you'd written this as `if x_distance < stop_distance: return` instead, what would break, specifically, on the very next frame?

**On collision layers**
- If Enemy shares the exact same collision layer as Player's own body (not Hitbox), what unintended interaction might that cause, beyond just "Hitbox doesn't detect it"?

---

## ✅ Final build steps — what to actually do, in order

**1. `player.gd`** — confirm this line exists inside `_ready()`:
```
add_to_group("players")
```

**2. `enemy.gd`** — full script: `EnemyType` enum, exported `speed`/`enemy_type`/`gravity`/`stop_distance`, `_ready()` with the `match` statement setting speed/tint per type, `_physics_process(delta)` applying gravity (gated by `is_on_floor()`), fetching nearest target, computing `abs()` horizontal distance, `if/else` on `stop_distance` to chase-or-hold, unconditional `move_and_slide()`, and `_get_nearest_player()` using the group + squared-distance min-search loop.

**3. Collision layers/masks** (editor): give `Enemy`'s root a distinct Layer; make sure Player's `Hitbox` Mask includes that layer.

**4. Delete `spawner.gd`** (editor, FileSystem dock): confirm via Find in Files that nothing references it, then delete. No `Spawner` node is needed.

**5. `main.gd`** — keep the existing `player_scene`/`enemy_scene` preloads and Player-instancing logic in `_ready()`. Remove the old manual `rattleSnake` test-instance block (position/add_child for it) now that spawning is interval-driven. Add the Timer's handler:
```
func _on_timer_timeout() -> void:
    var enemy: CharacterBody2D = enemy_scene.instantiate()
    enemy.enemy_type = randi_range(0, 1)
    add_child(enemy)
    var spawn_x: float = randf_range(-300.0, 300.0)
    enemy.position = Vector2(spawn_x, 0)
```
(Adjust the x-range and y-value to match your actual arena/floor position.)

**6. Editor — add the Timer to `Main`:**
1. Select `Main` root in the Scene dock.
2. **+** → search `Timer` → **Create** (it's now a sibling of `SharedCamera`, a child of `Main`).
3. Select `Timer`. Inspector: `Wait Time` → `2`, `One Shot` → unchecked, `Autostart` → checked.
4. Node dock → **Signals** tab → double-click `timeout()` → target `Main` → method name `_on_timer_timeout` → **Connect**.
5. Save (`Ctrl+S`).

**7. Run and confirm (F5/F6):**
- Enemies spawn on the timer interval, at varied x positions.
- Both tint colors/speeds appear across multiple spawns (proof the enum ordering is correct).
- Enemies fall under gravity if spawned above the floor.
- Each enemy's horizontal movement bends toward whichever co-op player is currently nearer — test by moving one player far away.
- Enemies stop closing distance (don't jitter/overlap) once within `stop_distance`, and correctly resume chasing if the player moves away again.
- Kicking an enemy via Player's `Hitbox` actually registers (confirms the collision layer/mask fix took effect).

---

**A design note worth sitting with, not fixing today:** `main.gd` now owns Player instancing, camera-follow math, and enemy spawning all in one file. That's a legitimate choice for where you are — but if it starts feeling like a junk drawer once Day 7 (water/game state), Day 8 (UI/win-lose), and Day 10 (networking) each want to add more, that's the signal to extract pieces into their own scripts, not "best practice" dictating it abstractly.

Report back what you actually observe on each item in step 7 — particularly whether both enemy types genuinely alternate, and whether the stop-distance behavior resumes chasing correctly — and I'll tell you if you're ready for Day 7.

📖 Keep reading your game mechanics/engine design book alongside today's build — nearest-target AI, data-driven enemy variants, and single-file-vs-split architecture are classic "systems thinking" topics that theory explains faster than trial-and-error code ever will.