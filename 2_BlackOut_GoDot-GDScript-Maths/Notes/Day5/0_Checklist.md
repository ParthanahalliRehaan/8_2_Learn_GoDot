# Day 3: Jump & Plunge (Vertical State)

By the end of today you'll be able to:
- Explain why `CharacterBody2D` does **not** apply gravity automatically the way `RigidBody2D` does, and how to simulate it yourself
- Distinguish a **one-shot impulse action** (Jump) from a **held/state-driven action** (Plunge)
- Build a simple state machine using `enum` + `match`
- Understand `clamp()` and why you need a fall-speed cap
- Understand `plunge_timer += delta` as the same accumulation pattern you'll see again and again — a rate applied over time
- Use `is_on_floor()` to gate jump logic correctly

## 🔤 GDScript (the language itself)

**`enum`**
- `enum State { IDLE, JUMP, PLUNGE }` — an `enum` is a named set of integer constants. Instead of tracking "what is the Ostrich doing" with a loose string or a magic number, you give each possibility a readable name. Under the hood `IDLE == 0`, `JUMP == 1`, `PLUNGE == 2`, but you never write the numbers — you write the names.

**`match`**
- `match current_state:` is GDScript's version of a switch statement. Each `State.X:` branch below it runs only when `current_state` equals that value. Compare this to a long chain of `if/elif/elif` — `match` says the same thing more readably once you have 3+ branches, which you now do.

**Typed state variable**
- `var current_state: State = State.IDLE` — note the type hint is the *enum's name itself* (`State`), not `int`, even though enums are secretly integers. This lets the editor autocomplete valid state names for you and catches typos.

**`clamp(value, min, max)`**
- A built-in function, not a method on a specific type. `clamp(velocity.y, -9999, max_fall_speed)` returns `velocity.y` unchanged *unless* it exceeds `max_fall_speed`, in which case it returns `max_fall_speed` instead. It's a hard ceiling (or floor).

**`is_on_floor()`**
- A method that already exists on `CharacterBody2D` — no need to declare it. It only returns accurate info *after* `move_and_slide()` has run at least once, which matters for where you place your jump-check logic.

---

## 🎮 Godot (engine & editor concepts)

**Gravity: ProjectSettings vs custom**
Godot's Project Settings → Physics → 2D → Default Gravity exists, but it's consumed automatically only by `RigidBody2D` (which the physics engine fully drives). `CharacterBody2D` is *you*-driven — nothing touches `velocity` unless your script does. So "gravity" for your Ostrich is really just: a constant you define yourself, added to `velocity.y` every physics frame. You *can* read the project setting via `ProjectSettings.get_setting(...)` if you want one source of truth shared with other systems, or just declare your own `@export var gravity: float` — either is valid; know that you're choosing, not being forced.

**One-shot vs held/state actions**
- Jump is **one-shot**: a single input press triggers a single instantaneous change (an upward velocity impulse), then physics (gravity) takes over — you don't hold jump to keep rising.
- Plunge is **state-driven**: pressing/holding it changes *what the Ostrich is doing* for a duration, tracked by a timer, and something different happens depending on how long that state has been active (per your GDD — plunge explodes past a threshold).

This distinction is *why* Plunge needs an `enum` state and Jump doesn't — Jump is a one-line velocity change, Plunge is a small state machine of its own.

---

## 📐 Math (this day's core)

**Gravity as constant acceleration**
Velocity is the rate of change of position. Acceleration is the rate of change of *velocity*. Gravity is acceleration — a constant downward pull that keeps adding to `velocity.y` every physics tick: `velocity.y += gravity * delta`. This is the same `* delta` pattern from Day 2 (direction × speed), just one derivative deeper: instead of `delta` scaling a *position* change, it's scaling a *velocity* change.

**Why the jump impulse is negative**
Recall from Day 2: Godot's Y axis points down. So "up" is negative Y. A jump impulse sets `velocity.y` to a large *negative* number (e.g. `-400`), and gravity's positive addition each frame gradually cancels it out and pulls it back down — that's what produces the natural rise-then-fall arc without you scripting the arc explicitly.

**Clamping = terminal velocity**
Real falling objects don't accelerate forever — air resistance caps them. `clamp()` is your cheap version of that: once `velocity.y` would exceed some `max_fall_speed`, you clamp it back down every frame. Without this, a long fall makes the Ostrich move faster and faster with no ceiling, which usually feels wrong and can even skip through collision shapes at high enough speed (tunneling).

**`plunge_timer += delta` — accumulation over time**
This is the *exact same pattern* as gravity's `velocity.y += gravity * delta`, applied to a different quantity. Instead of accumulating velocity, you're accumulating *elapsed time in a state*. Once `plunge_timer` crosses your GDD's threshold, something triggers (the explosion). Get comfortable seeing "some rate, or just `1`, times `delta`, added to a running total" as one general pattern — you'll use it constantly.

---

## 🤔 Doubts you should be asking yourself

**On gravity**
- If you never add anything to `velocity.y`, does the Ostrich float in place, or does `move_and_slide()` pull it down for you? (Check what you proved in Day 2 — did anything besides your own code ever touch `velocity`?)
- Where should the gravity addition happen — before or after you read input for jump? Does the order change the feel?

**On Jump**
- Should you be able to jump while already in the air (currently jumping or plunging)? What check prevents that — and is `is_on_floor()` enough on its own, or does it need to work together with your state enum?
- Is Jump itself a `State`, or is it just a velocity impulse that happens while `current_state` stays `IDLE`? (There's a real design choice here — argue it both ways before deciding.)

**On Plunge as a state machine**
- What triggers the transition *into* `State.PLUNGE` — a key press, or a key *held*? What's the practical GDScript difference between checking that once vs checking it every frame?
- What resets `plunge_timer` back to `0` — and if you forget to reset it, what bug do you think you'd see the *second* time a player plunges?
- Does `plunge_timer` belong on the `CharacterBody2D`/player script, or somewhere shared? (Hint: revisit your Day 1 GDD tag — is Plunge state [shared] or [per-player]?)

**On clamping**
- If you clamp `velocity.y` but forget to clamp it in *both* directions (only capping the fall, not a hypothetical upward runaway), is that a problem for this specific mechanic? Why or why not?
- Does `clamp()` change `velocity.y` permanently, or does it just return a new value you still have to assign back?

**On state transitions in general**
- When does `current_state` change back to `IDLE` — is that condition explicit in your code right now, or implicit ("nothing else sets it, so it just stays")?
- If Jump and Plunge could theoretically both be true at once with your current logic, what would happen? Is that actually possible given how you've written the checks?

---

## ✅ Instructions — Build Task (no code shown, you write all of it)

1. Open `scripts/player.gd`. Add a new exported float for `gravity` (a value like `900.0` to `1200.0` is a reasonable arcade-feel starting point — tune by feel later) and a `max_fall_speed` float (e.g. `800.0`).
2. Declare your `enum State` with at least `IDLE`, `JUMP`, and `PLUNGE`. Declare a variable `current_state` typed as `State`, defaulting to `State.IDLE`.
3. Declare a `plunge_timer` float, defaulting to `0.0`, and (from your GDD) a `plunge_explode_threshold` float.
4. Declare a `jump_velocity` float (e.g. `-400.0` — remember why it's negative from the math section above).
5. In `_physics_process(delta)`, *before* you call `move_and_slide()`: add gravity to `velocity.y` scaled by `delta`, then clamp `velocity.y` so it never exceeds `max_fall_speed`.
6. Add jump logic: if the jump input was just pressed **and** `is_on_floor()` is true, set `velocity.y` to `jump_velocity`.
7. Add plunge logic using `match current_state`: when the plunge input is held and conditions allow it (your call — grounded only? airborne only? check your GDD), transition `current_state` to `State.PLUNGE` and start incrementing `plunge_timer` by `delta` each frame that state is active.
8. When `plunge_timer` exceeds `plunge_explode_threshold`, trigger whatever "explode" placeholder you want for now (even just a `print()` statement is fine — the real explosion effect comes later) and decide what state it transitions to afterward.
9. Make sure `plunge_timer` resets to `0.0` at the correct point — reason through exactly where, using the doubt above.
10. Run the scene. Test: jumping from the ground, jumping mid-air (should fail), a long fall (confirm it doesn't accelerate past `max_fall_speed`), and holding plunge long enough to hit the threshold.

Report back what you built — particularly how you handled the IDLE↔JUMP↔PLUNGE transitions — and I'll tell you honestly whether your state machine is actually decoupled and correct, or just "happens to work" for the cases you tested.

📖 Keep reading your game mechanics/engine design book alongside this — today's state-machine pattern especially will click faster once you've seen it explained in theory, not just built once in GDScript.