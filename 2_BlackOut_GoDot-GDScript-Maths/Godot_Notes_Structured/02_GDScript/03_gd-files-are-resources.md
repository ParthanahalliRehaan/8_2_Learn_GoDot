# Why a .gd File Is a Resource

> **Source:** Day4/1_doubts.md (Doubt 7)  
> **Plan day:** Day 2

---

# Doubt 7: In Inspector when u click on a .gd file, why does this file appear under resources section. Does it mean that .gd file is a resource?

**Short answer: yes, genuinely — a `.gd` script *is* a Resource**, not just displayed like one.

**The inheritance chain (same concept as your `extends CharacterBody2D` question, one level down):**
```
Resource → Script → GDScript
```
`Script` is a subclass of `Resource`, and `GDScript` (what your `.gd` files actually are under the hood) is a subclass of `Script`. So by the same inheritance logic you just learned — "extends X means you inherit X's stuff" — a `.gd` file inherits everything a `Resource` is: something loadable via `load()`/`preload()`, assignable to a property slot, shareable across multiple nodes, and yes, it appears in resource-related UI like the Inspector's script slot.

**Why this actually makes sense conceptually:** a script is fundamentally just *data describing behavior* — code text that Godot loads and interprets, the same way a `RectangleShape2D` is data describing a shape, or a `Texture2D` is data describing pixels. None of these "do" anything on their own; they need to be *attached to* or *referenced by* a node to take effect. That's exactly the Resource pattern: standalone, node-independent data that something else points to.

**Concrete proof, if you want to verify it yourself:** in the Inspector, when a node has a script attached, click the script icon in the top bar — you get the same kind of context menu (Edit, Clear, Save As, Make Unique) you saw with your `RectangleShape2D`. That's not a coincidence or a UI similarity — it's literally the same Resource-handling machinery, because a `Script` genuinely is a `Resource` type.

**Connecting it back to something you already know:** this is also *why* `preload("res://scripts/player.gd")` is valid, legal code in Godot — you can preload a script the exact same way you'd preload a texture or a shape resource, because they're all just different flavors of the same base type.

**One-line takeaway:** in Godot, "Resource" isn't a special category some files belong to — it's the base class for *any* loadable, reusable, node-independent data, and scripts happen to be one specific kind of that data.
