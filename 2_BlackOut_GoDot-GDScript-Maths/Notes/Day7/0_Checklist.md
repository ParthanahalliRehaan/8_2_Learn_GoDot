# Day 5: Local Co-op — Two Ostriches, One Screen

By the end of today you'll be able to:
- Define two independent control schemes in Godot's Input Map (keyboard set A/B, or keyboard + gamepad device index)
- Turn your Day 2 `input_source` idea into something concrete enough to actually plug into two separate Player instances
- Instantiate a second player both by hand in the editor and by code
- Build a `Camera2D` that keeps two moving targets in frame using a midpoint and a distance-based zoom
- Understand why `Vector2.distance_to()` is the right tool here (unlike Day 6's snake AI, where you'll prefer `distance_squared_to()`)

---

## 🔤 GDScript (the language itself)

**Dictionary literal — `{}`**
A `Dictionary` is a set of key→value pairs, written `{ "key": value, "key2": value2 }`. This is the natural shape for `input_source`: something like `{ "left": "p1_left", "right": "p1_right", "jump": "p1_jump", "kick": "p1_kick" }`. The *keys* ("left", "right"...) stay the same for every player — they're the abstract actions your movement code cares about. The *values* are the actual Input Map action names, and those differ per player. Your movement code in `player.gd` should never say `"p1_left"` directly — it should look up `input_source["left"]`.

**Dynamic string lookup with `Input` functions**
`Input.is_action_pressed(action_name: String)` and `Input.get_axis(negative_action: String, positive_action: String)` both accept *variables*, not just string literals. So instead of `Input.get_axis("ui_left", "ui_right")` hardcoded, you write `Input.get_axis(input_source["left"], input_source["right"])`. Same function, but now the action names come from data instead of being baked into the code — this is the whole trick that makes one movement script work for two different players.

**`preload()` vs `load()` (needed if you instantiate by code)**
- `preload("res://scenes/player/Player.tscn")` loads the resource at *compile time* (when the script is parsed) and is the more common choice for a scene you know you'll always need.
- `load("res://scenes/player/Player.tscn")` loads it at *runtime*, useful when the path is dynamic.
Either way, what you get back is a `PackedScene` — a blueprint, not a live node yet.

**`.instantiate()`**
Called on a `PackedScene`, this actually creates a live node (and its whole child tree) in memory, ready to be added to the scene tree with `add_child()`. This is the code-equivalent of dragging a scene into the editor.

### Doubt 1, how does GoDot load and compile each scenes or nodes?
### Doubt 2, What is PackedScene, why we use it?
---

## 🎮 Godot (engine & editor concepts)

**Input Map — two control schemes, side by side**

*Editor way:* Project → Project Settings → Input Map tab. You add new **actions** (not keys directly) — e.g. `p1_left`, `p1_right`, `p1_jump`, `p1_kick`, and separately `p2_left`, `p2_right`, `p2_jump`, `p2_kick`. For each action, click the `+` to add an input event and press the actual key (WASD for P1, Arrow keys for P2, or whatever your GDD/comfort prefers). This is entirely editor-configured — no code needed to define the mapping itself.

*Code way:* You *can* add actions at runtime with `InputMap.add_action()` and `InputMap.action_add_event()`, but for a fixed two-player local setup this is unnecessary complexity — the editor's Input Map is the right tool here. (You'd only reach for the code version if you were building a runtime rebind-your-keys menu, which is out of scope for BlackOut.)

**Gamepad device index**
An `InputEvent` for a joypad button/axis carries a `device` property. `-1` means "any connected device," but for co-op you want to be specific: device `0` is the first controller Godot detects, device `1` is the second, etc. `Input.get_connected_joypads()` returns an array of currently connected device indices — useful for a "controller detected" check on your Host/Join-adjacent local setup menu (not required today, but know it exists).

*Editor way:* when adding a joypad button event to an action in the Input Map tab, there's a Device dropdown — set it to the specific index rather than "All Devices," or P1 and P2 will both trigger from either controller.

**Instancing a second Player: editor vs code**

*Editor way:* Open `Main.tscn`. Drag `Player.tscn` from the FileSystem dock into the scene tree twice — you now have two sibling nodes, e.g. `Player` and `Player2`. Rename them clearly. Position them apart in the 2D viewport so they don't spawn overlapping. This is the simplest path and perfectly fine for BlackOut.

*Code way:* In `Main.gd`'s `_ready()`, `preload` the Player scene once at the top of the script, then call `.instantiate()` twice, set each instance's `position` and its `input_source` dictionary, and `add_child()` each one. This is more setup but pays off later (Day 6's spawner, Day 10's networked spawning both need this pattern) — worth doing now for the practice, but not mandatory if you'd rather drag-and-drop today and revisit code-instancing later.

Either way, the important part isn't *how* the node got there — it's that **each instance gets assigned its own `input_source` dictionary** right after creation, whichever way you made it.

**Camera2D — node placement and built-in smoothing**
Where the camera lives matters: it must **not** be a child of either Player (it would just follow one of them). Add it as its own node, or as a child of `Main`, so it's free to track a computed point between both players.

Camera2D has an Inspector property group called **Smoothing**: `Position Smoothing Enabled` (bool) and `Position Smoothing Speed` (float). Turning this on makes the camera *editor-configured* — no `lerp()` code required — ease toward its target position instead of snapping. This is a genuine editor-vs-code choice: you can either lean on this built-in smoothing, or compute your own smoothed position in `_process()` with `lerp()` for more control. Try the built-in one first; it's simpler and does the job for BlackOut.

**Camera2D.zoom**
`zoom` is a `Vector2` (usually kept uniform, e.g. `Vector2(1.5, 1.5)`). The camera's visible world-area is roughly `viewport_size / zoom`. That means: **larger zoom values show *less* of the world (zoomed in), smaller zoom values show *more* of the world (zoomed out).** So to "zoom out as players get farther apart," you want zoom to *decrease* as the distance between players *increases* — the inverse relationship, easy to get backwards, so double-check your sign/direction when you test it.
### Doubt 1: So, When I instantiate a scene twice, so the code will be same for both, also if I do it the code way, I should make a main.gd right?
### Doubt 2: How to do the Camera thing in code?
---

## 📐 Math (this day's core)

**Midpoint**
The camera should aim at the average position of both players:
`target_position = (player1.position + player2.position) / 2.0`
This is just componentwise averaging of two vectors — Godot's `Vector2` supports `+` and `/` directly, so this reads almost exactly like the math notation.

**Distance between players — `Vector2.distance_to()`**
`player1.position.distance_to(player2.position)` returns a single float: the straight-line distance in pixels. This is the *actual* Euclidean distance (it does the square root internally), which is what you want here because you're going to feed this value into a zoom calculation where the real distance matters, not just a comparison. (Contrast: Day 6's nearest-enemy-targeting only needs to *compare* distances, so it'll use the cheaper `distance_squared_to()` instead — different job, different tool.)

**Turning distance into a zoom value**
You need some function that maps "distance between players" → "camera zoom." The simplest version: pick a `min_zoom` (most zoomed-in, for when players are close together) and a `max_distance` you expect players to ever reasonably be apart, then scale zoom down linearly as distance grows toward that max, clamping so it never zooms out further than some `min_zoom_out` value. This is the same `clamp()` tool from Day 3, now applied to a camera value instead of `velocity.y`. Don't worry about a "perfect" formula today — get something that visibly zooms out when players separate and zooms back in when they regroup, then tune the numbers by feel.

---

## 🤔 Doubts you should be asking yourself

**On the Input Map**
- If you accidentally bind the same key (say, Space) to both `p1_jump` and `p2_jump`, what happens when you press it — does *one* player jump, or *both*? Have you actually tested this, or are you assuming?
- Right now you're hardcoding two fixed schemes (P1 = keyboard set A, P2 = keyboard set B). What would break if you later wanted P2 to use a gamepad instead — does your `input_source` dictionary design already support that swap, or would you need to restructure it?

**On `input_source`**
- Your Day 2 script already reads from `input_source`. Does today's task require you to *change* any movement/jump/plunge/kick code at all — or only to change *what gets assigned* to `input_source` for each instance? If you find yourself editing movement logic today, did Day 2 actually decouple things correctly?
- Where does each Player instance's `input_source` get assigned — inside `player.gd` itself (hardcoded per scene?), or from the outside (`Main.gd` setting it after instancing)? Which one actually generalizes to Day 10's networked players?

**On the shared camera**
- If Player 1 stands still and Player 2 runs far away, the midpoint moves — but does it move at *half* Player 2's speed, or the same speed? Walk through the midpoint formula with real numbers to convince yourself.
- What happens at the exact moment the game starts, before either player has moved — is there a valid midpoint to compute, or could you hit a null-reference error if the camera's `_process()` runs before both players exist in the tree?
- You're clamping zoom so it doesn't zoom out forever. What's the actual failure mode if you *don't* clamp it — picture two players sprinting in opposite directions with no cap. Does that break gameplay, break visuals, or both?

**On smoothing**
- If you enable Camera2D's built-in position smoothing *and* separately write your own `lerp()` in code, what do you think happens — do the two stack, fight each other, or does one silently override the other? (You don't need to test this today, but you should be able to reason about why it'd be a bad idea to do both.)

---

## ✅ Instructions — Build Task (no code shown, you write all of it)

1. **Open Project Settings → Input Map tab.** Create eight new actions: `p1_left`, `p1_right`, `p1_jump`, `p1_kick`, `p2_left`, `p2_right`, `p2_jump`, `p2_kick`. Assign WASD (or your preferred set) to the `p1_*` actions and Arrow Keys + two other keys (e.g. Right Shift for kick, Enter/Numpad0 for jump) to the `p2_*` actions. Test each binding shows up correctly in the list before moving on, Also don't forget about plunge's input.

2. **Decide your `input_source` shape.** In `player.gd`, design (on paper or in a comment first) what keys your dictionary needs — at minimum `left`, `right`, `jump`, `kick` — mapping to Input Map action name strings. Confirm every place in `player.gd` that currently reads input (movement, jump, kick from Day 2–4) goes through this dictionary lookup rather than a literal string. If any spot still hardcodes an action name, fix that now — this is the actual point of today's task, not the camera.

3. **Instantiate the second player** using *either* method below (pick one, but read both so you understand the trade-off):
   - **Editor way:** In `Main.tscn`, drag a second copy of `Player.tscn` into the tree. Rename the two instances distinctly (e.g. `Player1`, `Player2`). Set distinct starting `position` values in the Inspector so they don't overlap.
   - **Code way:** In `Main.gd`, `preload` the Player scene, and in `_ready()`, call `.instantiate()` twice, set each instance's `position`, `add_child()` each, and keep a reference to both (e.g. in local variables or exported node references) for the camera script to use later.
```Detailed Instructions
## Standard Practice — Code-Way Instancing (Step by Step)

**Step 1 — Confirm/create `Main.gd` and attach it**
- Open `Main.tscn`.
- Click the **root node** of the scene (the top-level node — likely `Main` or `Node2D`, whatever you named it back on Day 0).
- Check the Inspector/top of the editor for whether a script is already attached (a small script icon next to the node name in the Scene dock means yes).
- If none exists: right-click the root node → **Attach Script** → keep language as GDScript, template "Default", path `res://scripts/Main.gd` (or wherever your Day 0 folder structure puts scripts) → **Create**.
- This script is where all instancing logic lives — it's the "director" of the scene, not something that goes on `player.gd`.

**Step 2 — Preload the Player scene, at the top of the script**
- At the very top of `Main.gd`, outside any function, declare a variable holding the `PackedScene`.
- Use `preload`, not `load` — you know the path at compile time and always need it, so `preload` is the correct choice here (recall the Doubt 1/2 distinction from earlier).
- Point it at wherever `Player.tscn` actually lives in your folder structure (e.g. `res://scenes/player/Player.tscn` — adjust to your real path).

**Step 3 — Declare variables to hold your two instances**
- Just below the preload line, declare two more variables (at the script level, not inside a function) to hold references to `player1` and `player2` once they exist.
- These need to be script-level (not local to `_ready()`) *specifically* because your camera script will need to reach them later — a variable local to `_ready()` disappears the moment that function ends.

**Step 4 — Write `_ready()`**
- Inside `_ready()`, call `.instantiate()` on your preloaded scene — once for `player1`, once for `player2` — assigning each result to the variables from Step 3.

**Step 5 — Set each instance's `position`**
- Immediately after instancing (still inside `_ready()`, before `add_child()`), set `.position` on each — pick two `Vector2` values far enough apart that they won't spawn overlapping. This mirrors what the editor way would've had you do in the Inspector.

**Step 6 — Assign each instance's `input_source`**
- This is the step that actually matters most for today's lesson. Set `.input_source` on each instance to its own dictionary — `p1_*` action names for `player1`, `p2_*` for `player2`.
- Do this *before* `add_child()` (recall the ordering doubt from earlier — if `player.gd`'s `_ready()` reads `input_source` for setup, you want it populated before the node enters the tree and its own `_ready()` fires).

**Step 7 — `add_child()` each instance**
- Call `add_child(player1)` and `add_child(player2)`. This is the moment they actually become part of the running game — visible, processing input, moving.

**Step 8 — Run the scene and verify via the Remote tree**
- Press Play. While it's running, in the Scene dock at the top-left you'll see a **Remote / Local** toggle appear — click **Remote**.
- Confirm you see two `Player` nodes listed there (this is the live proof they were actually added — nothing will show in the regular/Local view, since nothing was saved to `Main.tscn`).
- Test P1's keys move only `player1`, P2's keys move only `player2` — same verification as the editor way, Step 5 of the original task.

**Step 9 — Leave the references ready for the camera**
- Don't add anything else yet — just confirm `player1` and `player2` (the script-level variables from Step 3) are populated and holding the correct nodes. Your camera script, next, will read these two variables directly (or you'll wire them in via `@export`, if you make the camera reach into `Main` rather than the reverse) — that's why Step 3 mattered.

```

4. **Assign each instance its own `input_source`.** If you instantiated by editor, do this either via an `@export var input_source: Dictionary` on `player.gd` set differently per instance in the Inspector, or by having each instance run a small `_ready()` check on its own node name/index to pick P1 vs P2 mapping. If you instantiated by code, assign the dictionary directly after `.instantiate()`, before `add_child()`. Either approach is valid — the requirement is that **the exact same `player.gd` script drives both instances with different input**, nothing player-specific hardcoded inside the script itself.

5. **Run the scene with two players on screen.** Confirm P1's keys move only Player1, and P2's keys move only Player2, with no cross-talk (this directly answers the first Doubt above).

6. **Add a `Camera2D` node** as a sibling of the two players (not a child of either). Name it something clear like `SharedCamera`. In the Inspector, enable `Position Smoothing Enabled` and leave the default speed for now — you'll tune it after testing.

7. **Write a small script on the camera** (or reuse `Main.gd`) that, every `_process(delta)`:
   - Gets references to both player nodes (via exported `NodePath`/`@export var` node references set in the Inspector, or `get_node()` — your choice).
   - Computes the midpoint of their two `position` values using the formula from the Math section.
   - Sets the camera's `global_position` (or `position`, depending on where it sits in the tree) to that midpoint.

8. **Add the zoom-scaling logic.** Compute `distance = player1.position.distance_to(player2.position)`. Map that distance to a zoom `Vector2`, using `clamp()` so it never zooms in tighter than a minimum or out further than a maximum. Assign the result to `camera.zoom` each frame (or only when it changes meaningfully, if you want to optimize later — not required today).

9. **Playtest deliberately:** stand both players still and adjacent (camera should be tightly zoomed and centered on them both), then run them apart in opposite directions (camera should zoom out and keep both in frame), then bring them back together (camera should zoom back in smoothly, not snap).

10. **Sanity-check edge cases** from the Doubts section: what happens at scene start before movement, and whether your clamp values actually prevent an absurd zoom-out if players sprint to opposite corners of a large level.
```Rest Code from step 6 to 10
Since `Main.gd` already holds `player1`/`player2` as script-level references (from Step 3 of the instancing setup), the simplest path is to reuse `Main.gd` for the camera logic too — exactly what the instructions allow ("Write a small script on the camera, or reuse `Main.gd`"). No need for a separate camera script or exported `NodePath`s when the references already live right there.

**Step 1 — Add the Camera2D node (editor)**
- Open `Main.tscn`.
- Right-click the **root node** → Add Child Node → `Camera2D`.
- Rename it `SharedCamera` in the Scene dock.
- Confirm it sits as a **sibling** of your instancing logic's parent — i.e. directly under `Main`, not nested inside either player (it can't be, since the players don't even exist in the editor tree right now — they're code-instanced — but double-check you didn't accidentally drop it under some other leftover node).
- In the Inspector, find **Camera2D → Smoothing** and check `Position Smoothing Enabled`. Leave the speed at default for now.

**Step 2 — Reference the camera and update it in `Main.gd`**
      extends Node2D
      # Main.gd
      
      var player_scene: PackedScene = preload("res://scenes/player/Player.tscn")
      
      var player1: CharacterBody2D
      var player2: CharacterBody2D
      
      @onready var shared_camera: Camera2D = $SharedCamera
      
      # --- Zoom tuning knobs ---
      @export var min_zoom: float = 1.5     # most zoomed-in (players close together)
      @export var max_zoom_out: float = 0.6 # most zoomed-out (players far apart)
      @export var max_distance: float = 800.0  # distance at which zoom hits max_zoom_out
      
      func _ready():
      	player1 = player_scene.instantiate()
      	player2 = player_scene.instantiate()
      
      	player1.position = Vector2(300, 100)
      	player2.position = Vector2(600, 100)
      
      	player1.input_source = {
      		"left": "p1_left", "right": "p1_right",
      		"jump": "p1_jump", "plunge": "p1_plunge", "kick": "p1_kick"
      	}
      	player2.input_source = {
      		"left": "p2_left", "right": "p2_right",
      		"jump": "p2_jump", "plunge": "p2_plunge", "kick": "p2_kick"
      	}
      
      	add_child(player1)
      	add_child(player2)
      
      
      func _process(delta: float) -> void:
      	# Sanity-check (Step 10): both players must exist before we touch their .position.
      	if not player1 or not player2:
      		return
      
      	# --- Step 7: midpoint tracking ---
      	var midpoint = (player1.position + player2.position) / 2.0
      	shared_camera.global_position = midpoint
      
      	# --- Step 8: distance-driven zoom ---
      	var distance = player1.position.distance_to(player2.position)
      	var t = clamp(distance / max_distance, 0.0, 1.0)
      	var zoom_value = lerp(min_zoom, max_zoom_out, t)
      	shared_camera.zoom = Vector2(zoom_value, zoom_value)

**Why it's structured this way, tied back to the Doubts:**

- **`if not player1 or not player2: return`** answers Doubt from Step 10 directly — since `Main.gd`'s own `_ready()` instances both players *before* `_process()` ever runs, this guard is technically unreachable in your current setup (both always exist by frame 1). It's still worth keeping: the moment you refactor toward Day 10's networked spawning, players won't both exist at `_ready()` time anymore, and this line is what prevents a null-reference crash the instant that changes.
- **`global_position`, not `position`** — since `SharedCamera` sits directly under `Main` (not nested inside any other transformed node), these are equivalent here, but `global_position` is the safer habit going forward in case your tree gets deeper later.
- **Zoom direction:** `t = 0` (players adjacent) → `zoom_value = min_zoom` (zoomed **in**, e.g. `1.5`). `t = 1` (players at `max_distance` or beyond) → `zoom_value = max_zoom_out` (zoomed **out**, e.g. `0.6`). Double-check this against the Doc's warning: **larger zoom = more zoomed in.** `min_zoom` should be your *largest* number, `max_zoom_out` your *smallest* — that's deliberately counter-intuitive naming, so re-read the two `@export` values above carefully before you tune them.

**Step 9 — Playtest checklist:**
- Both idle, adjacent → camera tight and centered.
- Run apart → camera zooms out, keeps both in frame, follows the midpoint (not snapping to either player individually).
- Regroup → camera eases back in smoothly (that's `Position Smoothing Enabled` doing its job, not a `lerp()` you wrote — confirm you didn't accidentally add a second one, per the earlier "do both smoothing systems stack or fight" doubt).

Run it and report back what actually happens — specifically whether the zoom direction came out right on your first try, since that's the easiest sign flip to get backwards.
```
Report back what you built — specifically how you structured `input_source` for two players and how you're computing/clamping the camera zoom — and I'll tell you honestly whether the decoupling actually holds (i.e., could you drop in a *third* input scheme tomorrow without touching `player.gd`) or whether it's held together by per-player special-casing that'll bite you on Day 10.

### Doubt 1: In each step, if there is a code way make a comparison which is better and why?
### Doubt 2: Why do we need to create the player using editor way, and not code way? Whats the benefit?
### Doubt 3: How does lerp works?