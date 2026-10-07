# Day 7 — Water, Hallucination & Shared Game State

Today's topic is bigger than it looks. You're not just adding a water meter — you're building the first piece of state in this game that isn't *owned* by any single node. Player owns its own position. Enemy owns its own target. But "how much water does the team have left" doesn't belong to Player 1, or Player 2, or the HUD, or the spawner — everyone needs to read it, multiple things need to write to it, and it has to survive scene changes. That's a different architectural problem than anything you've solved so far.

---

## 🔤 GDScript

**Autoload / Singleton**
A regular script attached to a node only exists while that node is in the tree. An **Autoload** is registered in Project Settings so Godot instantiates it once, automatically, before any scene loads, and keeps it alive for the entire game — accessible from anywhere by its global name, no `get_node()` path-hunting required. This is how you get one source of truth that Player, HUD, and Cacti can all talk to without needing references to each other.

**Dictionaries as per-entity storage**
You already know arrays and single variables. A `Dictionary` (`{}`) maps a key to a value — here, `player_id` → that player's water/health. This is what makes a *single* autoload script scale to 1 player or 2 players without you writing separate variables (`water_p1`, `water_p2`) that duplicate logic.

**Custom signals**
`signal water_depleted(player_id)` — you declare your own signal, exactly like the built-in `body_entered` you connected on Day 4. You `emit_signal("water_depleted", id)` (or `water_depleted.emit(id)` in Godot 4 syntax) from inside the autoload when the condition is met, and anything that cares (HUD, game-over logic) connects to it — without the autoload needing to know who's listening. This is the actual decoupling test for today: does `game_state.gd` know or care what happens when water hits zero? It shouldn't.

**Probability with `randf()`**
`randf()` returns a float between 0.0 and 1.0. `if randf() < 0.02:` succeeds roughly 2% of the time it's checked. The *shape* of your check (every physics frame vs. on a timer) changes what that 2% actually means in practice — that's this day's math concept.

---

## 🎮 Godot

**Registering an Autoload**
Project Settings → Autoload tab → point at your script (e.g. `scripts/game_state.gd`) → give it a Node Name (e.g. `GameState`) → Add. From then on, any script in the project can call `GameState.something()` directly, no preload, no path.

**Autoload lifetime vs scene lifetime**
An Autoload persists across scene changes (e.g. Main → GameOver → Main again) unless you explicitly reset it. That's a feature here (state survives a pause menu) but a trap if you don't reset water/health when a new run starts — worth thinking about now, not on Day 8 when you build the Start Menu.

**Connecting to a signal from an Autoload**
Same Node dock → Signals tab workflow as `body_entered`, except now the signal source is a script-defined one on a singleton, not a built-in on a physics node. If it's not showing in the dock the way built-ins do, you may need to connect it in code (`GameState.water_depleted.connect(_on_water_depleted)`) instead — that's one of today's doubts to work through, not something I'm answering for you.

---

## 📐 Math

**Rate-based depletion**
`water -= rate * delta` — same delta-time pattern as Day 2's movement and Day 3's gravity, just applied to a scalar instead of a vector. The reason it's still `* delta` and not a flat `water -= rate` per frame: frame rate isn't guaranteed constant, so a flat subtraction would drain water faster on a high-refresh monitor than a low one. Delta-time keeps the drain rate tied to real seconds, not frame count.

**Probability checks: delta-driven vs fixed-interval**
Two different ways to ask "does this random event happen":
1. **Delta-scaled probability** — the chance itself is scaled by delta, so it stays roughly consistent per *second* regardless of frame rate (this is subtler than it looks — a naive `randf() < 0.02` checked every physics frame at 60fps fires far more often per second than the same check on a `Timer` that ticks once per second).
2. **Fixed-interval check** — a `Timer` ticks once every N seconds, and you roll the dice once per tick. Frame-rate-independent by construction, easier to reason about, but less granular.

Your GDD calls this hallucination — you'll need to decide which of these two shapes you actually want, and be able to explain *why* a naive per-frame `randf()` check without delta-scaling is a bug, not a feature (it silently ties your hallucination frequency to the player's frame rate).

---

## 🤔 Doubts you should be asking yourself

**On the Autoload itself**
- If `GameState` is a Dictionary keyed by `player_id`, what initializes the entries for however many players are actually in the current run — where does that happen, and when, relative to Player instancing?
- What happens if something reads `GameState.water[player_id]` for a `player_id` that was never added to the dictionary?

**On shared vs. per-player**
- Go back to your Day 1 GDD tag for water. If it's `[shared]`, does the dictionary-per-player design from the plan even still make sense, or does shared water want a completely different data shape (a single float, not a dictionary)?
- If water is shared, what happens to Player 2 when Player 1 drains it by running into a Cactus — should that even be possible, and does your GDD already answer this?

**On custom signals and decoupling**
- Right now, does `game_state.gd` need to know *anything* about the HUD, or about what "game over" means, in order to correctly emit `water_depleted`? If you find yourself writing HUD-update logic or scene-change logic inside `game_state.gd`, what does that tell you about where the responsibility actually belongs?
- Could two different systems (HUD *and* a game-over check) both connect to the same `water_depleted` signal independently, without knowing about each other? Why does that matter?

**On hallucination and probability**
- If you check `randf() < 0.02` inside `_physics_process`, roughly how many times per second is that check even running, and does that match the "occasional, unpredictable" feel your GDD is going for — or would it trigger constantly?
- Is hallucination itself `[shared]` or `[per-player]` per your Day 1 tags, even though the water it might be tied to could be `[shared]`? These are two separate flags — don't assume they match.

**On Cacti**
- A Cactus trades health for water — is that trade a one-time interaction (Area2D `body_entered`, consume, `queue_free()` the cactus) or something continuous while overlapping? Which fits "sacrifice a cactus for water" better?

---

## ✅ Build Task — step by step, no code yet

1. **Create `scripts/game_state.gd`.** Register it as an Autoload named `GameState` in Project Settings.(Exact path is settings->project settings->global,here in hero section you can see autoload,click on it)

2. **Design the data shape first, on paper, before writing a line of GDScript.** Pull up your Day 1 GDD. For each of water/health and hallucination, write down: shared or per-player? If per-player, what's the key (player_id) and what's the value (float for water, or a small dictionary of {water, health} per player)? If shared, is it just one variable?

3. **Declare your state variables** in `game_state.gd` to match the shape you just decided — not before.

4. **Declare a custom signal** for water hitting zero (name it whatever fits your GDD's language — `water_depleted`, `player_dehydrated`, your call), and any other state-transition signal you think today's mechanics need.

5. **Write a function that depletes water over time**, called from `_process(delta)` (this lives in the autoload itself, since it's the autoload's own state) — using the rate-times-delta pattern above. Have it emit your signal when a given player's (or the shared) water crosses zero.

6. **Build the Cactus scene** (`Area2D`) — decide one-shot vs continuous overlap per the doubt above, and on interaction call into `GameState` to trade health for water rather than manipulating the numbers from inside the Cactus script directly.

7. **Implement hallucination** using whichever probability shape (delta-scaled vs Timer-based) you decided fits after working through the math section — triggered from `GameState`, but the actual "flip my movement input" logic should live in `player.gd`, reacting to something `GameState` exposes (a signal, or a per-player flag it reads). Think about where the line is: `GameState` should own *whether* hallucination is active, not *how* a player's movement responds to it.

8. **Connect at least one listener** to your `water_depleted` signal — doesn't need to be the final HUD/game-over logic yet (that's Day 8), just a `print()` is enough today to prove the signal path works end to end.

Report back: what shape you chose for water (shared vs. per-player, and why), whether your probability check for hallucination is delta-scaled or timer-based and why you picked it, and whether `game_state.gd` currently knows anything about the HUD or game-over flow that it shouldn't. That last one is the real test of today.

📖 Keep reading your game mechanics/engine design book alongside this — global/shared state and event-driven signals (vs. tight coupling between systems) are exactly the kind of thing that clicks faster from theory than from trial and error in the editor.