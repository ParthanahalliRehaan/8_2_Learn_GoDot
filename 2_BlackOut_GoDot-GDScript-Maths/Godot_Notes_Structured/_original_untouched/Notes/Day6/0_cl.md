# Day 4: Kick & Collision (Area2D) — Consolidated, Correctly Mapped

A kick hitbox needs pure detection with zero physics interference — that's why it's `Area2D`, not the other two.

**Collision Layer vs Mask:**
- **Layer = "what am I"** — the category this node broadcasts about itself.
- **Mask = "what do I listen for"** — the categories this node checks against.

A signal fires when one node's Mask matches the other's Layer at the same bit slot. The *name* you type (`enemy`, `player_hitbox`) is just a label on that slot — Godot compares the underlying bit, not the string.

**Project Settings → 2D Physics, not 2D Render:** both show identical Layer 1–20 lists under "Layer Names," but 2D Render controls visibility/draw order, 2D Physics controls collision detection. Only 2D Physics names appear on `Area2D`'s Layer/Mask checkboxes.

---

## 📐 Math Notes

**Why powers of 2:** each layer slot claims one distinct bit. Layer *n* → bit value `2^(n-1)`: Layer 1 = 1, Layer 2 = 2, Layer 3 = 4, Layer 4 = 8. This lets one integer represent combinations via addition (`1 + 4 = 5`), and lets the engine check overlap with a single bitwise AND instead of comparing a list one by one. If you ever set `collision_layer`/`collision_mask` directly in code instead of via checkboxes, this is the conversion you're doing by hand.

**Overlap detection is geometric, not distance-polling:** `Area2D` does real shape-vs-shape overlap tests each physics step and only *notifies* you via signal when state changes (enters/exits) — you're not writing a manual distance check in `_physics_process`.

---

## 🤔 Doubts (yours to keep testing, not just read)

- If Hitbox were accidentally `RigidBody2D` or `CharacterBody2D` instead of `Area2D`, what visibly breaks? (You tested this — gravity pulling it in the viewport with zero code referencing it.)
- Is Layer/Mask matching one-directional or bidirectional? Does the enemy also need its own Mask pointed at `player_hitbox`, or is Hitbox's Mask → Enemy's Layer enough on its own? — **still an open experiment**, worth running once you have a placeholder or real enemy.
- If two things overlap the hitbox in the same frame, does the callback fire once or twice? What would that imply for code that isn't written to handle being called more than once?
- Should `monitoring` ever be `true` outside the kick window? What problem would leaving it always-on cause once Kick has real wind-up/recovery animation frames?
- If Player is freed, does Hitbox (as a child) need manual cleanup, or does the scene tree handle it?

---

## ✅ Instructions — Correct Build Order

**1. Create `Hitbox.tscn`**
- FileSystem dock → right-click `scenes/player/` → **New Scene**. Scene dock → **Other Node** → search `Area2D` → set as root → rename `Hitbox`. Save as `scenes/player/Hitbox.tscn`.
- Select `Hitbox` → **+** → search `CollisionShape2D` → Add. Inspector → Shape dropdown → **New RectangleShape2D** → drag handles in viewport to size/position it over the Ostrich's kick range.
- No code equivalent — scene creation is editor-only.

**2. Instance `Hitbox.tscn` as a child of `Player.tscn`**
- Open `Player.tscn` → right-click Player root → **Instantiate Child Scene** → select `Hitbox.tscn`.
- No practical code equivalent for a permanent child like this.

**3. Assign Layer & Mask**
- Project Settings → General → **Layer Names → 2D Physics** (not 2D Render). Name a slot `player_hitbox`, another `enemy`.
- Select `Hitbox` → Inspector → **Layer**: check `player_hitbox` only. **Mask**: check `enemy` only.
- Code alternative (more error-prone, save for later): `hitbox.collision_layer = <bit value>` / `hitbox.collision_mask = <bit value>`, computed via `2^(n-1)` for whichever slot number you actually used.

**4. Set `monitoring` to `false` by default**
- Select `Hitbox` → Inspector → **Area2D → Area → Monitoring** → uncheck. Editor-only — this is the scene's baseline/rest state; code only flips it at runtime later.

**5. Get a reference to the Hitbox in `player.gd`**
- `@onready var hitbox = ` then drag the `Hitbox` node from the Scene dock into the script right after the `=` — Godot inserts the exact correct `$Path`.

**6. Connect `area_entered`**
- Editor way: select `Hitbox` → Node dock → Signals tab → double-click `area_entered(area: Area2D)` → target Player → Connect. Auto-generates the empty callback stub.
- Code way: in `_ready()`, `hitbox.area_entered.connect(_on_hitbox_area_entered)` — no parentheses on the function name. You write the empty stub yourself.
- Limitation to remember: code-way connections never show in the (Local) Signals tab — that tab only reflects scene-file-saved connections. Verify code-way connections either by the `print()` in step 8, or by switching Scene dock to **Remote** tab while the scene is running.

**7. Build the actual Kick trigger — this was the missing piece**
Nothing in steps 1–6 ever turns `monitoring` on, so without this, step 8's callback will never fire no matter how correctly it's written. Following your existing `player.gd` patterns (state enum, `input_source` dict, delta-accumulator):
- Project Settings → Input Map → add action `ui_kick`, bind a key.
- `input_source["kick"] = "ui_kick"` in `_ready()`.
- Add `KICK` to your `State` enum.
- Add `@export var kick_duration: float = 0.2` and `var kick_timer: float = 0.0`.
- In `_physics_process`, on `Input.is_action_just_pressed(input_source["kick"])` (guarded against re-triggering mid-kick): set `current_state = State.KICK`, `kick_timer = 0.0`, `hitbox.monitoring = true`.
- Add a `State.KICK` branch in your `match`: accumulate `kick_timer += delta`, and once it crosses `kick_duration`, set `hitbox.monitoring = false` and return to `IDLE`.

**8. Write the callback**
- Replace the `pass` stub with `print(area.name)` (or `print("Hit: ", area.name)`) — `.name` gives you a readable string instead of an object reference.

**9. Toggle `monitoring` on/off around the Kick window**
- Already built in Step 7 (Path A / delta-accumulator style). **Path B alternative** if you want the practice: add a `Timer` child under Hitbox, Wait Time = kick duration, One Shot checked, connect `timeout` (same editor/code choice as step 6) to set `monitoring = false`; you still set `monitoring = true` and call `timer.start()` when Kick begins.

**10. Test it**
- Add a placeholder `Area2D` + `CollisionShape2D` named `TestTarget` somewhere in your level — **and give it the `enemy` Layer**, or your Mask won't match it and you'll get the same silent no-output symptom.
- Run (F6), overlap it, press Kick → confirm `print()` fires.
- Negative test: overlap it *without* Kicking → confirm nothing prints. This is the one that actually proves `monitoring` is scoped correctly, not just coincidentally positioned.

---

Report back once Step 10 passes both the positive and negative test — that closes out Day 4 for real.
