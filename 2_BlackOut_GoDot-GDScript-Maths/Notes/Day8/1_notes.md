## 🔤 GDScript — functions & concepts used today

**`add_to_group(group_name: String)`**
Instance method, called on `self` inside a script (e.g. `add_to_group("players")` in `player.gd`'s `_ready()`). Tags *this specific node* with an arbitrary string label. Takes exactly one argument — a `String`, nothing else. Doesn't return anything. You can call it multiple times with different strings to put one node in several groups at once.

**`get_tree().get_nodes_in_group(group_name: String) -> Array`**
`get_tree()` returns the `SceneTree` — the object that manages every node currently active in the running game. `.get_nodes_in_group("players")` asks it for every node anywhere that's been tagged with that exact string via `add_to_group()`. Returns an `Array` — empty if no matches, never `null` itself (the array can just have zero elements). This is why checking `.is_empty()` matters more than checking for `null` here.

**Why groups matched incorrectly earlier:** the string passed to `add_to_group()` and the string passed to `get_nodes_in_group()` must be character-for-character identical — same case, same spelling, same plural/singular. Godot does zero validation or fuzzy-matching; a mismatched string just silently returns an empty array, no error thrown. That silence is the actual danger — it fails quietly instead of loudly.

**`abs(value)`**
Built-in global function (not a method on a specific type — you call it as `abs(x)`, not `x.abs()`). Returns the magnitude of a number, stripping the sign: `abs(-7) == 7`, `abs(7) == 7`. Works on both `int` and `float`. You needed this because `target.global_position.x - global_position.x` gives a *signed* difference (negative if target is to the left), but "how far apart" should only care about magnitude.

**`preload(path: String)` vs `load(path: String)`**
Both return the actual `Resource` at that path (a `PackedScene`, in our case). `preload` resolves **at parse time** — before the game even runs, baked in when the script compiles — so it's instant and cached. `load` resolves **at the moment that line executes**, useful only when the path itself needs to be computed dynamically at runtime. We used `preload` throughout since `Enemy.tscn`'s path never changes.

**`instantiate()`**
Called on a loaded `PackedScene` (what `preload`/`load` gave you): `EnemyScene.instantiate()`. Builds an actual live `Node` (and its full child hierarchy) from that scene's blueprint, in memory — but it is *not yet part of the running game* until you separately call `add_child()` on it.

**`add_child(node: Node)`**
Instance method — attaches the given node as a child of whatever node called it (`self`). This is the exact moment the new node officially enters the `SceneTree`, and critically, the exact moment `_ready()` fires on it and any of its children. Everything about ordering-sensitivity (enemy_type, groups, etc.) traces back to this one fact.

**`enum` and `match`**
`enum EnemyType { RATTLESNAKE, SAHARAN_VIPER }` declares a small closed set of named integer constants. `match enemy_type:` with branches like `EnemyType.RATTLESNAKE:` is GDScript's switch-statement equivalent — cleaner than chained `if/elif` when branching on one variable's possible values.

**`if / else` vs `if ... return` — the resume-checking distinction**
`_physics_process(delta)` runs fresh, every single physics frame, unconditionally, from the top. An `if x_distance >= 5: return` pattern *skips the rest of that frame's logic* but re-evaluates from scratch next frame — it is **not** a permanent state change, so it self-corrects automatically as distance changes. This only breaks if you introduce a persistent flag (like `is_stopped = true`) that never gets reset — we deliberately avoided that trap by using `if/else` to set `velocity.x` directly rather than `return`ing early, so gravity (`move_and_slide()`) still runs every frame regardless of chase state.

## 🎮 Godot — engine concepts used today

**Groups**
A pure tagging system, nothing more. A "group" isn't a node type, a class, or anything Godot tracks semantically — it's a text label you invent, stuck onto nodes via `add_to_group()`, queried via `get_nodes_in_group()`. Godot has zero built-in groups; every one you use (`"players"`, `"enemies"`, whatever) is something you made up and must spell consistently everywhere.

**`_ready()` timing relative to `add_child()`**
`_ready()` is a lifecycle callback the engine calls automatically the instant a node (and its children) enter the `SceneTree` — which happens *during* the `add_child()` call, before that line of your calling code even finishes. Practical consequence: any property your `_ready()` logic depends on (like `enemy_type` driving a `match` statement) must be set **before** `add_child()`, or `_ready()` will have already run against whatever default value the variable had.

**`is_on_floor()`**
`CharacterBody2D` built-in method. Returns `true` if the body's collision shape is currently resting against a surface Godot's physics considers "floor-facing" (based on collision normal angle, floor detection settings). Used to gate whether gravity should keep accumulating on `velocity.y` — without this check, gravity would just keep adding downward force even while already grounded, which can cause jitter or sinking.

**`Timer` node + `timeout` signal**
A node you place in the tree with `wait_time`, `autostart`, and `one_shot` properties. Emits its `timeout` signal on every countdown completion (repeating, since `one_shot` is off). You connect that signal — via the editor's Signals tab — to a handler function, same mechanism as `body_entered`/`area_entered` back on Day 4. Signals are Godot's general-purpose "something happened, react to it" system; you've now used the identical pattern three separate times (collision, group setup, timer) — worth noticing that's not a coincidence, it's the engine's dominant idiom.

**`@export` on typed values (including enums and arrays)**
Exposes a script variable in the Inspector panel for that specific node instance, so you can tune values (or, for `Array[Vector2]`, populate a list of spawn points) without touching code, and differently per instance if you have multiples of the same scene.
