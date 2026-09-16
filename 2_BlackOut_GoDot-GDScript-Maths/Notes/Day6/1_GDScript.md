# One Bitmask for Collision layer, one for Collision mask
```MD
**Layer = "what am I"** ✅ correct.
**Mask = "what am I looking for"** ✅ correct.

**"Both together represent *the* bitmask for a node"** — this is the one part to adjust. They're not one combined bitmask — they're **two separate, independent bitmasks**, each its own integer, each doing a different job:

- `collision_layer` — one bitmask, entirely on its own. Answers "what am I."
- `collision_mask` — a second, separate bitmask, entirely independent from the first. Answers "what do I check for."

A node has **both**, side by side, not merged into one number. Nothing links them together internally — you could set a node's Layer to `5` and its Mask to `12` and they have zero relationship to each other; they're just two different integers living on the same node, each read by the engine for a different purpose during the overlap check.

**Where they *do* meet — but only between two different nodes, never within one:** the actual collision check compares **Node A's Mask** against **Node B's Layer** (and separately, Node B's Mask against Node A's Layer, if bidirectional matching is being used). That comparison is the only place these numbers interact — and it's always *my mask vs your layer*, never *my mask vs my own layer*.

So the corrected version of your sentence: **"Layer and Mask are each their own separate bitmask on a node — Layer says who I am, Mask says who I'm listening for, and detection happens when one node's Mask bit matches another node's Layer bit."**
```
## Bitmask Analogy: A row of light switches on a wall

Imagine a wall with 16 light switches in a row, each one labeled with a sticker: `1`, `2`, `3`, `4`... up to `16`. Those stickers are just so **you** know which switch is which when you're flipping them by hand. That's it. That's all the sticker number means.

Now, behind the wall, each switch is wired to a **different-sized battery**, and the sizes aren't random — they double each time:
- Switch labeled `1` → wired to a battery worth **1**
- Switch labeled `2` → wired to a battery worth **2**
- Switch labeled `3` → wired to a battery worth **4**
- Switch labeled `4` → wired to a battery worth **8**
- Switch labeled `5` → wired to a battery worth **16**

**You never see these battery numbers.** You only ever see the sticker (`1`, `2`, `3`, `4`...). The battery values are hidden inside the wall.

---

## Mapping this to Godot exactly

- The **checkbox in the Inspector, labeled "4"** = the switch with the sticker `4` on it. That's what your screenshot shows. 100% real, 100% what you click.
- When you check that box, Godot flips that switch on — and internally, invisibly, that adds the number **8** to a hidden total (because switch "4" is wired to battery-value 8, from `2^(4-1) = 8`).
- You never had to know it was 8. You just clicked the box labeled 4.

## Where does the number 8 ever actually show up, then?

**Only if you stop clicking boxes and start writing code instead.** If you write:
```gdscript
hitbox.collision_layer = 8
```
...that line, in code, means exactly the same thing as checking the box labeled "4" in the Inspector. Same switch, same result — just two different ways of flipping it. The Inspector lets you say "flip switch 4" by clicking. Code makes you say "set the hidden total to 8" by typing the number directly.

---

**So to directly answer your screenshot confusion:** the box labeled "4" is real, correct, exactly what you clicked. I was never disputing that box exists — I was talking about the hidden battery-value (8) behind it, which is a totally separate number you'd only ever type if you used code instead of clicking.

---

# `$` — what it actually does & onready?

**`$` is shorthand syntax for `get_node()`.** These two lines are 100% identical in behavior — `$` is purely a shorter way to type the second one:
```gdscript
@onready var hitbox = $Hitbox
@onready var hitbox = get_node("Hitbox")
```

**What `get_node()`/`$` does:** it looks up a node in the scene tree by its path, relative to the node the script is attached to, and returns a reference to it — essentially "go find this specific node and hand me a pointer to it so I can call methods/read properties on it."

**Path rules, since this is where mismatches happen later:**
- `$Hitbox` — a direct child named exactly `Hitbox`.
- `$Hitbox/CollisionShape2D` — a grandchild, one level deeper (child of Hitbox).
- `$"../SomeSibling"` — a node that isn't a descendant at all, using `..` to go up first (rare for you to need yet).

The name after `$` **must match the Scene dock's node name exactly**, including capitalization — this is exactly why dragging the node into the script (rather than typing it) is safer: Godot writes the real current name for you instead of you typing from memory and risking a stale/wrong name if you ever renamed the node.

**Why `@onready` has to wrap it (tying back to what you already know):** `$Hitbox` only resolves successfully once the node tree actually has a `Hitbox` child present. `@onready` is what delays this lookup until right before `_ready()`, when that's guaranteed true — without it, `$Hitbox` could try to run before the child exists and fail.