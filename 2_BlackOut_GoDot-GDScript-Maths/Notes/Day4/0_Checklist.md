# Day 2: Player-Agnostic Movement(Day 1 done i.e, GDD improvements)

By the end of today you'll be able to:
- Explain the difference between `_ready()`, `_process(delta)`, and `_physics_process(delta)`, and know which one movement code belongs in and why
- Build a `CharacterBody2D` scene with a properly aligned `CollisionShape2D` and visual placeholder
- Understand what `velocity` and `move_and_slide()` actually do under the hood
- Design an `input_source` structure that decouples "what direction to move" from "where input comes from" — the pattern that lets Day 5 (local co-op) and Day 10 (networked players) plug in without touching your movement math
- Understand `Vector2` as direction + magnitude, and why `velocity = direction * speed` works without you multiplying by `delta` yourself
- Notice (and understand the cause of) the diagonal-movement-is-faster bug, ahead of fixing it with `.normalized()`

## 🔤 GDScript (the language itself)

**Variable declarations**
- `var speed: float = 300.0` — `var` declares a variable, `: float` is an optional *type hint* (Godot lets you skip it, but hints catch bugs early and give you autocomplete), `= 300.0` is the initial value.
- `@export var speed: float = 300.0` — the `@export` annotation exposes that variable in the **Inspector** panel when you select the node, so you can tweak `speed` per-instance without touching code. Try it once your script is attached: you'll see a "Speed" field appear in the Inspector.

**Functions**
- `func _get_direction() -> Vector2:` — `func` declares a function, `-> Vector2` is the *return type hint* (this function promises to give back a `Vector2`).
- Functions starting with `_` (like `_ready`, `_process`) are Godot's naming convention for **virtual/lifecycle methods** — ones the engine calls automatically, not ones you call yourself. A function you write and call yourself (like `_get_direction()`) technically doesn't need the underscore, but it's common style for "private/internal helper."

**Dictionaries**
- `var input_source: Dictionary = {}` — a dictionary is key→value pairs, like `{"left": "ui_left", "right": "ui_right"}`. You look up a value with `input_source["left"]`, which gives you `"ui_left"` — a *string* that happens to be a Godot input action name. This indirection is the whole trick: your movement code says "give me whatever action is mapped to `left`" instead of "I only ever check `ui_left`."

**Built-in Input functions you'll use**
- `Input.get_axis(negative_action, positive_action)` — returns a float from -1 to 1. Feed it two action name *strings*.
- `Input.is_action_pressed(action_name)` — returns true/false for a single action, useful when building direction from four separate checks instead of two axes.

---

## 🎮 Godot (engine & editor concepts)

**Node types you used today**
- `CharacterBody2D` — has built-in `velocity: Vector2` property and a `move_and_slide()` method. Calling `move_and_slide()` reads `velocity`, moves the body that amount (scaled by the physics delta internally), and automatically stops/slides you along anything solid it hits.
- `CollisionShape2D` — invisible; defines the *physical* boundary Godot uses for collision detection. Without one, `CharacterBody2D` has no shape and won't collide with anything.
- `ColorRect` — purely visual, no collision. This is your placeholder until Day 12's `AnimatedSprite2D`.

**Why CollisionShape2D and ColorRect both need matching size/position**
The collision shape is invisible to you but not to the physics engine; the ColorRect is visible to you but invisible to physics. If they don't line up, you'll *see* the rectangle in one spot but it'll *collide* as if it's somewhere else — a classic beginner bug source, so get in the habit of checking alignment now.

**Project Settings → Input Map**
This is where action *names* (like `"ui_left"`) get bound to actual keys/buttons. Right now you're pointing at Godot's built-in `ui_*` actions — later (Day 5) you'll likely add your own custom actions like `p1_left`, `p2_left` so two local players don't fight over the same keys.

**The Inspector**
Whenever you `@export` a variable, it shows up here per-node-instance. This matters more than it sounds: if you later have 2 Ostrich instances in a scene, each can have a different `speed` value set in the Inspector without editing the script.

---

## 📐 Math (this day's core)

**Vector2 as direction + magnitude**
A `Vector2(x, y)` describes movement or position in 2D. `(1, 0)` = pure rightward, `(0, -1)` = pure upward (Godot's Y axis points *down*, screen-coordinate style — this trips up a lot of beginners coming from math class where up is positive).

**Combining directions**
`Input.get_axis("ui_left", "ui_right")` gives you an X component from -1 to 1. Do the same for Y with up/down, and combine: `Vector2(x_axis, y_axis)`. If you're pressing right *and* down simultaneously, you get `(1, 1)` — notice that's *longer* than `(1, 0)` alone (magnitude √2 ≈ 1.41 vs 1). That means diagonal movement is faster unless you normalize.

**Normalization** (you'll want this once you test diagonal movement)
`.normalized()` on a Vector2 rescales it to length 1 while keeping its direction. So `Vector2(1,1).normalized()` ≈ `(0.707, 0.707)` — same diagonal direction, but now magnitude 1, so multiplying by `speed` gives you *consistent* speed in every direction. Worth testing both ways yourself to *see* the diagonal-speed bug before fixing it — that'll cement why normalization matters more than me just telling you.

**velocity = direction × speed, and why `move_and_slide()` doesn't need `delta`**
`CharacterBody2D.move_and_slide()` already multiplies your `velocity` by the physics delta internally — that's part of what makes it convenient over rolling your own `position += velocity * delta`. So `velocity` should be in **pixels per second**, not per-frame — that's why `speed = 300.0` means "300 pixels every second," and the engine handles converting that into "however many pixels this particular physics tick."

---

## 🤔 Doubts you should be asking yourself

**On lifecycle functions**
- If I put `move_and_slide()` inside `_process()` instead of `_physics_process()`, would it still basically work? (Yes, often — but why is it still the wrong choice? Think about frame-rate consistency across different machines.)
- Does `_ready()` run again if I re-enter the scene, or only once ever per game launch?

**On CharacterBody2D**
- What happens if I forget the `CollisionShape2D` entirely — does the node error out, silently do nothing, or move through walls?
- Is `velocity` a property I set directly, or does something else calculate it for me? (Check: who actually assigns a value to `velocity` in your script?)

**On the input_source design**
- If `input_source` is a `Dictionary` mapping `"left"` → `"ui_left"`, what type is the *value* actually — a string, or something else? What does `Input.get_axis()` expect as its arguments?
- Where should `input_source` get populated — inside `_ready()`, or as the default value in the variable declaration? Does it matter?
- If I hardcode `Input.get_axis("ui_left", "ui_right")` directly inside `_get_direction()` instead of pulling the action names from `input_source`, have I actually solved today's problem, or just moved the hardcoding one function over? (This is the one to be honest with yourself about.)

**On the math**
- Does `Vector2.ZERO` returned from `_get_direction()` actually stop the character, or does it leave the last velocity applied forever?
- When no keys are pressed, what should `_get_direction()` return, and does your current code actually do that?
- Will your rectangle currently move faster diagonally than in a straight line? Don't fix it yet if so — just notice whether it happens.

**On the scene structure**
- Does the order of `CollisionShape2D` vs `ColorRect` as children of `Player` matter, or is it purely cosmetic in the Scene tree?
- If I resize the `ColorRect` later, does the `CollisionShape2D` resize with it automatically, or are they completely independent?

---

## ✅ Instructions — Build Task (no code shown, you write all of it)

1. Build `Player.tscn` under `scenes/player/`: root node `CharacterBody2D` named `Player`, with a `CollisionShape2D` child (give it a `RectangleShape2D`, ~32x32) and a `ColorRect` child (same size, positioned so it visually overlaps the collision shape).
2. Attach a script to the `Player` root, save as `scripts/player.gd`.
3. In the script: declare an exported `speed` float, and a `input_source` variable (your choice of type, but it must hold *mappable* input info, not literal `Input.get_axis()` calls).
4. Write a function that computes a movement direction *from* `input_source` — not from hardcoded action name strings sitting inline in that function.
5. In `_physics_process(delta)`, use that direction to set `velocity`, then call `move_and_slide()`.
6. Confirm in Project Settings → Input Map that the actions you're referencing (`ui_left`, `ui_right`, `ui_up`, `ui_down`, or your own custom ones) actually exist and are bound to keys.
7. Run the scene (F6) and test movement in all 4 directions, then test diagonal movement and just *observe* the speed.

That frustration is normal — there's a real gap between "I understand the concept" and "I can produce the syntax from nothing," and that gap closes with more scaffolding, not more theory. Let's close it today with very literal, line-by-line instructions — I'll tell you exactly what each line should *say* in plain English, you translate it into GDScript syntax yourself.

## Script structure — line by line, in order(Detailed only for beginners, who are feeling that they are not capable of reading docs and writing the code)

**Line 1:** Your script needs to declare that this script extends `CharacterBody2D` (this must be the very first line, no exceptions — it's how Godot knows what base functionality this script inherits).

**Line 2 (blank line for readability, optional)**

**Line 3:** Declare an exported variable named `speed`, typed as a float, with a default value of `300.0`. Recall the pattern: `@export` annotation, then `var`, then the name, then `: type`, then `= value`.

**Line 4:** Declare a variable named `input_source`, typed as a `Dictionary`, with an empty dictionary `{}` as its default value. No `@export` needed for this one — it's internal.

**Line 5 (blank line)**

**Line 6:** Start a function definition for `_ready()`. Recall: `func`, name, `()`, `-> void:` (since it returns nothing).

**Line 7 (indented inside _ready):** Assign to `input_source` a dictionary literal with four key-value pairs: key `"left"` → value `"ui_left"`, key `"right"` → value `"ui_right"`, key `"up"` → value `"ui_up"`, key `"down"` → value `"ui_down"`. Recall dictionary literal syntax: curly braces, `key: value` pairs separated by commas.

**Line 8 (blank line)**

**Line 9:** Start a function definition for `_get_direction()`, no parameters, returning `-> Vector2:`.

**Line 10 (indented):** Declare a local variable `x`, assign it the result of calling `Input.get_axis()`, passing two arguments — not literal strings, but `input_source["left"]` and `input_source["right"]` (dictionary lookups by key, using square brackets).

**Line 11 (indented):** Same idea for `y` — `Input.get_axis(input_source["up"], input_source["down"])`.

**Line 12 (indented):** `return` a `Vector2` constructed from `x` and `y` — `Vector2(x, y)`.

**Line 13 (blank line)**

**Line 14:** Start a function definition for `_physics_process(delta: float) -> void:` — note this one *does* take a parameter, `delta`, typed float.

**Line 15 (indented):** Assign to `velocity` (this property already exists on `CharacterBody2D`, you're not declaring it) the result of calling `_get_direction()` multiplied by `speed`.

**Line 16 (indented):** Call `move_and_slide()` — no arguments, nothing assigned, just call it as a statement on its own line.
