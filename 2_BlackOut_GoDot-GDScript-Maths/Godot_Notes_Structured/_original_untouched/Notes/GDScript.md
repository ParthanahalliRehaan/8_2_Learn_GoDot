# Some information about gdscript like how to comment, what does this line means will be here!
## GDScript is baby of TS and Python!

## How to comment, we use # for commenting, there is no dedicated character for multi line commenting in GDScript

## What does `extends CharacterBody2D` (or `extends Node2D`) actually mean?

**Core idea: inheritance.** When your script says `extends CharacterBody2D`, you're telling Godot "this script *is a* CharacterBody2D, plus whatever extra code I write here." Your script doesn't replace or wrap a CharacterBody2D — it literally *becomes* one, with all of CharacterBody2D's built-in properties (`velocity`, `position`, etc.) and methods (`move_and_slide()`, etc.) available to you immediately, without you writing any of that yourself.

**Why this matters practically:** without `extends CharacterBody2D`, none of your `velocity = ...` or `move_and_slide()` code would work — those aren't things you invented, they're inherited from the base class. `extends` is what grants you access to them.

**The chain goes deeper than one level.** Godot's node types form a hierarchy, and each type extends the one above it:
```
Node → Node2D → CollisionObject2D → PhysicsBody2D → CharacterBody2D
```
So a `CharacterBody2D` isn't *just* movement + collision code — it also inherits everything `Node2D` gives it (like `position`, `rotation`, `scale`, since it exists in 2D space), and everything plain `Node` gives it (like existing in the scene tree at all, having a `_ready()`/`_process()` lifecycle, being able to have child nodes). Each step up the chain adds more specific capability on top of the more general stuff below it.

**Comparing `extends Node2D` vs `extends CharacterBody2D`:**
- `Node2D` gives you: position, rotation, scale, being visible/drawable in 2D space — but *no* physics or collision behavior at all. If you wrote `extends Node2D` for your Player, you'd have no `velocity` property, no `move_and_slide()`, nothing — you'd have to build movement and collision handling completely from scratch.
- `CharacterBody2D` gives you everything `Node2D` does, *plus* physics-body-specific stuff: `velocity`, `move_and_slide()`, built-in collision response. This is why you extend `CharacterBody2D` for Player — you want that inherited movement/collision machinery for free.

**Practical test for yourself:** if you tried writing `velocity = ...` in a script that says `extends Node2D` instead of `extends CharacterBody2D`, what do you think would happen — would it work, or would Godot give you an error saying `velocity` doesn't exist? (This is worth actually trying once, briefly, just to see the error message — seeing the failure mode cements *why* the right `extends` matters more than being told.)

**One-line summary:** `extends X` means "give my script all of X's existing properties/methods/behavior as a starting point, and I'll add my own custom logic on top of that in this file."

## @export explanation
```GDScript
# @export allows you to adjust this value directly from the Inspector.
# Note: Changing it in the Inspector only affects the instance in your scene,
# not the default value defined in this script.
@export var speed:float = 200.0
```

## 🚀 Advanced Dictionary Usage in GDScript

### 🔑 Declaring & Initializing
```gdscript
# Empty dictionary
var my_dict = {}

# With initial values
var config = {
    "fullscreen": true,
    "resolution": Vector2(1920, 1080),
    "volume": 0.8
}
```

---

### 🧩 Adding & Updating
```gdscript
# Add new key-value
config["language"] = "English"

# Update existing key
config["volume"] = 0.5
```

---

### 🗑 Removing
```gdscript
config.erase("fullscreen")   # Removes key "fullscreen"
```

---

### 🔍 Querying & Checking
```gdscript
if config.has("resolution"):
    print("Resolution is set to:", config["resolution"])

# Safe lookup with default
print(config.get("theme", "DefaultTheme"))
```

---

### 🔄 Iteration
```gdscript
for key in config.keys():
    print(key, ":", config[key])

# Or directly
for key in config:
    print(key, ":", config[key])
```

---

### ⚡ Useful Methods
```gdscript
config.clear()                # Removes all entries
print(config.size())          # Number of key-value pairs
print(config.keys())          # Returns array of keys
print(config.values())        # Returns array of values
```

---

### 🧠 Advanced Operations
```gdscript
# Merge dictionaries
var defaults = {"volume": 1.0, "language": "English"}
config.merge(defaults)   # Adds missing keys, keeps existing ones

# Deep merge (overwrites existing keys too)
config.merge(defaults, true)

# Duplicate dictionary
var copy = config.duplicate()

# Nested dictionaries
var player = {
    "stats": {"hp": 100, "mp": 50},
    "inventory": {"gold": 200, "items": ["sword", "shield"]}
}
print(player["stats"]["hp"])   # Access nested value
```

---

### 🧮 Practical Example
```gdscript
# Example: tracking enemies
var enemies = {
    "orc": {"hp": 80, "attack": 15},
    "goblin": {"hp": 50, "attack": 10}
}

# Update goblin’s HP
enemies["goblin"]["hp"] -= 20

# Add new enemy
enemies["dragon"] = {"hp": 300, "attack": 50}

# Remove orc
enemies.erase("orc")
```

---

✅ **Summary:**  
Dictionaries in GDScript are not just simple key-value stores — they support merging, duplication, nested structures, and safe lookups. They’re perfect for managing **game state, configuration, inventories, or any structured data**.

# What is Input class, Vector2 Data type, normalization, get_axis?
## 🎮 What `Input.get_axis` Does
- `Input` is a **global singleton class** in Godot, not part of `CharacterBody2D`.  
- `get_axis(action_negative, action_positive)` checks two input actions (like `"ui_left"` and `"ui_right"`).  
- It returns:
  - `-1` if the negative action is pressed (e.g., left arrow).  
  - `+1` if the positive action is pressed (e.g., right arrow).  
  - `0` if neither is pressed.  
  - Smooth values between `-1` and `+1` if using analog input (like a joystick).  

👉 So yes, it’s how Godot reads **your input from keyboard, mouse, or controller**.

---

## ✏️ What `.normalized()` Does
- `normalized()` is a **method of `Vector2`**.  
- It scales the vector so its **length (magnitude)** becomes `1`, but keeps the same direction.  
- Why? Without normalization, diagonal movement would be faster (since `(1,1)` has length √2 ≈ 1.41).  
- Normalizing ensures consistent speed in all directions.

Example:
```gdscript
var v = Vector2(3, 4)
print(v.length())        # 5
print(v.normalized())    # (0.6, 0.8) → length = 1
```

---

## 🧩 What Is `Input`?
- `Input` is a **built-in Godot singleton class**.  
- It handles all input: keyboard, mouse, touch, gamepad.  
- You don’t need to create it — it’s always available globally.  
- Common methods:
  - `Input.is_action_pressed("jump")`
  - `Input.get_axis("left", "right")`
  - `Input.get_vector("left", "right", "up", "down")`

---

## 🧩 What Is `Vector2`?
- `Vector2` is a **built-in data type** in Godot, not tied to `CharacterBody2D`.  
- It represents a 2D vector (x, y).  
- Used for positions, directions, velocities, etc.  
- Common methods:
  - `.length()` → magnitude of the vector  
  - `.normalized()` → unit vector  
  - `.dot(other)` → dot product  
  - `.angle()` → angle in radians  

👉 `CharacterBody2D` uses `Vector2` a lot because movement, velocity, and physics are all 2D vectors.

---

✅ **Summary:**  
- `Input` → global class for handling player input.  
- `get_axis` → reads two opposite actions and returns a value between -1 and 1.  
- `Vector2` → built-in math type for 2D vectors.  
- `.normalized()` → ensures consistent direction with unit length.  

# what does this _ before name of function represent?
In Godot, the **underscore prefix (`_`)** before a function name has a special meaning:

---

## 🔎 What `_` Before a Function Name Represents
- Functions starting with `_` are usually **callbacks** or **lifecycle methods** that Godot calls automatically at certain times.  
- Examples:
  - `_ready()` → runs when the node enters the scene tree.  
  - `_process(delta)` → runs every frame.  
  - `_physics_process(delta)` → runs every physics frame.  
- You can also define your own functions with `_` (like `_get_direction()`), but by convention, the underscore suggests it’s **internal/private** to the script, not meant to be called from outside.

---

## 📘 Key Distinction
- **Godot-defined callbacks**: Must have the underscore (e.g., `_ready`, `_process`).  
- **Your own functions**: You can name them with or without `_`. Using `_` is just a convention to mark them as internal helpers.  

---

✅ **Summary:**  
The underscore doesn’t change how the function works — it’s a naming convention. In Godot, many built-in lifecycle methods use `_` because the engine automatically calls them. For your own functions, `_` simply signals “this is private/internal,” while functions without `_` are more like public utilities.

# move_and_slide() & velocity meaning
## The `velocity`/`move_and_slide()` — explained again, standalone

**`velocity` — what it is, how it works**
It's a `Vector2` property that already exists on every `CharacterBody2D` node the moment you write `extends CharacterBody2D` — you're not creating it, you're *setting* a slot the engine already gave you. By default it's `Vector2.ZERO` (not moving). It does absolutely nothing by itself — it's inert data, just a "what direction and speed am I supposed to be going" value, sitting there until something reads it.

**`move_and_slide()` — what it actually does**
That "something" that reads `velocity` is `move_and_slide()`. When called (in Godot 4, no arguments needed), it:
1. Reads the current `velocity`
2. Calculates how far to actually move this physics tick (factoring in delta internally, which is why you never multiply by delta yourself here)
3. Checks along that path for collisions (using your `CollisionShape2D`)
4. If it hits something solid, it slides along the surface instead of stopping dead or clipping through
5. Physically updates the node's actual position, and may adjust `velocity` itself if a collision changed the resulting motion

So the division of labor is: **you decide *intent*** (by setting `velocity`), **the engine handles *execution*** (via `move_and_slide()` — the actual moving, colliding, sliding).

---

## Will you be able to move after the fix — yes, if the fix is applied correctly

Your logic in `_get_direction()` and `_ready()` was already correct. The *only* thing standing between you and a moving rectangle was that one broken line. Once you change:
```gdscript
velocity = move_and_slide(velocity)
```
to just:
```gdscript
move_and_slide()
```
— save the script, open `Player.tscn` (make sure it's the *scene* open, since F6 runs the currently active scene), and press F6. Your ColorRect should now visibly slide around when you press arrow keys/WASD.
