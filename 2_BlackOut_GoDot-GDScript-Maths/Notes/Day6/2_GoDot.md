# What `area_entered` actually is — and clearing the bitmask doubt

## Is `area_entered` a function, a node, or something else?

**None of those exactly — it's a *signal*.** A signal is Godot's built-in event-broadcasting mechanism. Think of it as a named "announcement" a node can make when something happens to it. It's not a node (it doesn't live in the Scene tree, doesn't have a position, doesn't appear as its own item you click on), and it's not a function you call yourself — it's something the *engine* emits automatically, at a specific moment, on your behalf.

**Where does `area_entered` come from?** It's built into the `Area2D` class itself (inherited from `CollisionObject2D`), same as `speed` or `gravity` are things `CharacterBody2D` gives you for free. You didn't write it — Godot's engine code emits it internally, every physics step, whenever its shape-overlap check detects a new `Area2D` has started overlapping.

**So what does "connect" actually mean?** A signal by itself does nothing — it just fires into the void unless something is *listening*. "Connecting" a signal means registering a function to be called automatically whenever that signal fires. That's the whole relationship:
- `Hitbox` (an `Area2D`) → **emits** `area_entered` whenever something overlaps it.
- `player.gd` (attached to your Player node) → **listens** by connecting a function (`_on_hitbox_area_entered`) to that signal.
- When the signal fires, Godot automatically calls your connected function, passing along whatever data the signal carries (here: the `Area2D` that entered, as the `area` parameter).

**Why connect it from Player's script and not write it directly inside a `Hitbox.gd` script?** You *could* give `Hitbox.tscn` its own script and connect/handle the signal there instead — that's a valid alternative structure. But since your Kick logic (state, timer, monitoring toggle) already lives in `player.gd`, keeping the response to detection in the same script keeps everything about Kick's behavior in one place, rather than splitting related logic across two separate scripts for no strong reason yet.