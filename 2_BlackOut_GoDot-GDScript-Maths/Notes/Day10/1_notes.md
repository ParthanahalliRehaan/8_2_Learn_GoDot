## 🔤 GDScript

**Custom signals** — `signal water_changed(player_id, new_water, max_water)` declares a signal with a defined parameter list, just like a function signature. You `emit_signal("water_changed", id, value, max)` wherever the actual change happens (inside `add_water`, `drain_water`), and any number of other scripts can `.connect()` a function to it. The emitting code never needs to know who's listening — that's the decoupling point of today's lesson.

**Connecting signals in code vs the editor** — you did both today. In the editor (Cactus's `body_entered`), Godot auto-generates the stub and wires it via the Node dock. In code (`GameState.health_changed.connect(_on_health_changed)`), you connect manually in `_ready()`. Same mechanism, two entry points.

**Signal handler signature must match the emit** — `_on_health_changed(id: int, new_health: float, max_health: float)` takes exactly the three arguments you passed to `emit_signal`. Mismatch the count/order and it'll fail silently or error at connect time.

**The `id != player_id: return` guard** — every signal handler you wrote today starts with this. Since `GameState` is a global singleton, its signals fire once and *every* connected Player instance receives the callback — including the one it's not about. Without the guard, player2's health bar would react to player1's Cactus visit too. This is the practical cost of centralizing state: broadcast is global, so each listener has to filter for itself.

**`await get_tree().create_timer(duration).timeout`** — pauses execution *inside that function* for `duration` seconds without blocking the rest of the game (physics, other players, rendering all continue). This is how `_on_hallucination_started` waits 3 seconds before calling `end_hallucination`, without needing a separate `Timer` node or a manually incremented counter.

**Dictionary access via dot-notation** — `players[player_id].water` works because in GDScript, dictionary string/key access and dot access are interchangeable when the key is a valid identifier (`p.water` is shorthand for `p["water"]` once `p` holds that dict).

**`const` vs `var` for tunables** — `WATER_DRAIN_RATE`, `MAX_WATER` are `const` because they're fixed game rules, not per-instance state that changes at runtime. Contrast with `@export var speed` on Player, which *is* meant to vary and be tweaked per instance in the Inspector.

**Deleting unused code, not just adding** — today you removed `try_hallucination_check()`, `HALLUCINATION_CHANCE`, and `hallucination_check_timer` once the trigger logic changed. A signal-driven system especially rewards this — dead code that emits or listens to nothing is genuinely inert, not just untidy.

## 🎮 Godot

**Autoload/Singleton (`GameState`)** — registered once in Project Settings, accessible from any script via its node name with no reference-passing. This is *why* signals could connect across completely separate scenes (Player, Cactus, Enemy) without any of them holding a direct reference to each other — they all just talk to the one global node.

**`ProgressBar`** — you added a second one (`WaterBar`) as a sibling of `HealthBar`, same node type, same `.max_value`/`.value` API. Today reinforced that a UI node's job is purely to *display* a value pushed to it — it holds no game logic of its own.

**`Area2D` + `body_entered` signal (Cactus)** — built the full pipeline: collision shape defines the trigger zone, `body_entered(body: Node2D)` fires when something with a matching collision layer enters it, and `queue_free()` on the Cactus makes the interaction one-time (the node is scheduled for deletion after the current frame).

**Groups (`add_to_group("players")`)** — this is how `enemy.gd`'s `_get_nearest_player()` finds all players without `Enemy` needing direct node references to `Player1`/`Player2`. Same decoupling principle as signals, different mechanism — worth noticing both exist for a reason: signals for "notify me when X happens," groups for "give me everyone tagged Y right now."

**`preload()` + `instantiate()` for runtime spawning** — you did this for the Cactus today the same way you already had it for Player and Enemy: `preload` loads the `.tscn` blueprint once at compile time, `.instantiate()` creates a live node from it, `add_child()` puts it in the tree.

Report back once you've traced through the mid-hallucination-Cactus edge case from last message — that's still open, and it's a good one to actually work out rather than guess at.

📖 Keep reading your game mechanics/engine design book alongside this.