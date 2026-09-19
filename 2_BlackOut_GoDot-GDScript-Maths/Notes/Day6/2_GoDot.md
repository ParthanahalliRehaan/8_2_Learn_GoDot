# What `area_entered` actually is — and clearing the bitmask doubt

## Is `area_entered` a function, a node, or something else?

**None of those exactly — it's a *signal*.** A signal is Godot's built-in event-broadcasting mechanism. Think of it as a named "announcement" a node can make when something happens to it. It's not a node (it doesn't live in the Scene tree, doesn't have a position, doesn't appear as its own item you click on), and it's not a function you call yourself — it's something the *engine* emits automatically, at a specific moment, on your behalf.

**Where does `area_entered` come from?** It's built into the `Area2D` class itself (inherited from `CollisionObject2D`), same as `speed` or `gravity` are things `CharacterBody2D` gives you for free. You didn't write it — Godot's engine code emits it internally, every physics step, whenever its shape-overlap check detects a new `Area2D` has started overlapping.

**So what does "connect" actually mean?** A signal by itself does nothing — it just fires into the void unless something is *listening*. "Connecting" a signal means registering a function to be called automatically whenever that signal fires. That's the whole relationship:
- `Hitbox` (an `Area2D`) → **emits** `area_entered` whenever something overlaps it.
- `player.gd` (attached to your Player node) → **listens** by connecting a function (`_on_hitbox_area_entered`) to that signal.
- When the signal fires, Godot automatically calls your connected function, passing along whatever data the signal carries (here: the `Area2D` that entered, as the `area` parameter).

**Why connect it from Player's script and not write it directly inside a `Hitbox.gd` script?** You *could* give `Hitbox.tscn` its own script and connect/handle the signal there instead — that's a valid alternative structure. But since your Kick logic (state, timer, monitoring toggle) already lives in `player.gd`, keeping the response to detection in the same script keeps everything about Kick's behavior in one place, rather than splitting related logic across two separate scripts for no strong reason yet.

---

# Doubts
**Doubt**
I created a separate `Hitbox.tscn` scene and instanced it into `Player.tscn` — couldn't I have just added the `Area2D` as a plain child node inside `Player.tscn` directly, instead of making it its own scene?

**Answer**
Yes, technically you could — Godot doesn't force it. But a separate scene is the better habit because:
- **Reusable** — the same `Hitbox.tscn` can be instanced into Player, Enemy, or anything else later without copy-pasting node structure.
- **Independently testable** — you can open and tweak the hitbox's `CollisionShape2D` in isolation, without the rest of Player's tree in the way.
- **Clean signal ownership** — `hitbox.gd` owns one job (detect overlap, emit a signal); `player.gd` just listens, keeping movement/jump/plunge logic untangled from collision logic.
- **Idiomatic composition** — nesting small focused scenes into bigger ones is Godot's version of components; it's overkill only for truly one-off, non-reusable nodes.

---

**Doubt**
My `Hitbox` needs *swapped* collision layer/mask values in Player (layer 1, mask 2) vs. Enemy (layer 2, mask 1). If I change the layer/mask on the hitbox, won't that value change automatically everywhere it's used — i.e., won't editing Player's hitbox also change Enemy's?

**Answer**
No — it won't cascade. Changing a property (like `collision_layer`/`collision_mask`) on the `Hitbox` *instance* inside `Player.tscn` creates a **local override** for that one instance only (a small revert-arrow icon appears next to the property once overridden). It does **not** write back to the base `Hitbox.tscn` file.

So:
- `Hitbox` instance in `Player.tscn` → layer 1, mask 2 (set on that instance)
- `Hitbox` instance in `Enemy.tscn` → layer 2, mask 1 (set on that instance)

Each instance's override is independent of the other and independent of the base scene. The only way a change *does* propagate to all instances is if you edit the property directly on `Hitbox.tscn` opened by itself — and even then, only instances that *haven't* already locally overridden that specific property inherit the new default; overridden instances stay as set.