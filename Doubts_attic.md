# Doubts
## Doubts 1: 3D Basics & File Formats

| Doubt | One-Sentence Answer |
|-------|-------------------|
| **D1** — What is `.glb` and how to make it? | `.glb` is the binary version of `.gltf`, a 3D model format that bundles mesh, materials, and animations into one file; export it from Blender via **File → Export → glTF 2.0 (.glb)**. |
| **D2** — Are 3D models in Godot scenes? Why? How? | Yes, when you import a `.glb`, Godot converts it into a **PackedScene** (a reusable scene tree of MeshInstance3D, AnimationPlayer, etc.) so you can instance it, script it, and modify it like any other scene. |
| **D3** — What is a sprite? | A sprite is a 2D image or animation displayed on screen; in 3D, a sprite becomes a **billboard mesh** (a flat plane that always faces the camera). |
| **D4** — Why `.glb` for 3D but `.png/.svg` for 2D? | 3D models contain complex hierarchical data (meshes, bones, materials) that `.glb` handles structurally, whereas 2D art is just flat raster/vector images with no scene graph. |

---

## Doubts 2: Display & Rendering

| Doubt | One-Sentence Answer |
|-------|-------------------|
| **D1** — What is aspect ratio? | Aspect ratio is the proportional relationship between width and height (e.g., 16:9), ensuring your game displays correctly on any screen without distortion. |
| **D2** — What is stretch mode? | Stretch mode controls how the game viewport scales to fit the window; **"viewport"** with **"keep"** aspect renders to a fixed-size buffer first, then scales it while preserving aspect ratio. |
| **D3** — What is anti-aliasing? | Anti-aliasing is a rendering technique that smooths jagged diagonal edges by blending pixel colors along boundaries. |
| **Filter (Nearest vs Linear)** | **Nearest** preserves sharp pixel edges (ideal for pixel art); **Linear** blurs pixels when scaling (smoother for high-res art). |
| **Compression** | Texture compression reduces GPU memory usage by encoding images into GPU-friendly formats (like VRAM Compressed), trading slight quality for performance. |
| **Mipmaps** | Mipmaps are pre-generated smaller versions of a texture used when objects are far away, preventing visual noise and improving rendering speed. |

---

## Doubts 3: Scenes, Nodes & Scripts

| Doubt | One-Sentence Answer |
|-------|-------------------|
| **D1** — Why separate `src/player.tscn` from `scenes/main.tscn`? | `Player.tscn` is a **reusable blueprint** of what a player is; `Main.tscn` is the **level composition** that instances the player into the world. |
| **D1** — How do we connect them? Do we use signals? | You connect them by **instancing** `Player.tscn` as a child of `Main.tscn` in the editor or via code; signals are for **event communication** (like `player_died`), not for structural connection. |
| **D2** — Why `extends Resource`? What is this "object"? | `extends Resource` creates a **data-only blueprint** (like `PlayerStats`) that stores values but has no position in the scene tree; the **object** is the runtime instance created when you load or `.new()` that resource. |
| **D2** — Scene + Script + Object flow? | A **Scene** is a saved node tree, a **Script** defines behavior for one node, and the **Object** is the live instance running in memory when the scene is instantiated. |
| **D3** — When you run a scene, is the first child run first? | Godot runs `_ready()` on **children first, parent last**, so by the time the root node's `_ready()` fires, all its children are fully initialized and usable. |
| **D4** — Does `_init()` need to be called manually? | No, `_init()` is the **constructor** that runs automatically when you call `.new()` or instantiate a scene; you never call it directly. |
| **D1** — Can a main scene have multiple scenes? | Yes, `Main.tscn` can instance many sub-scenes (Player, Enemies, UI) as its children, composing the full game level. |

---

## Doubts 4: Signals, Resources & Node Types

| Doubt | One-Sentence Answer |
|-------|-------------------|
| **D1** — What does `signal` mean in code? | A `signal` is a **broadcasted event** declared on a node (like `health_changed`) that other nodes can **connect to** and **react to** when `emit()` is called. |
| **D2** — What is `test_entity.tres`? | `.tres` is a **text-based Resource file** that stores data (like stats, inventory, or config) outside of scenes, editable in Godot's inspector and shareable across nodes. |
| **D1** — What is `extends Node`, `extends Resource`, etc.? | `extends` declares what **base class** your script inherits from — `Node` for scene-tree objects, `Resource` for data containers, `CharacterBody2D` for physics-driven 2D actors — each giving different built-in behavior. |
| **D2** — What is a signal practically? | A signal is **decoupled communication**: when something happens (e.g., player takes damage), the source node `emit`s a signal, and any connected node runs its callback without the source knowing who listens. |
| **D3** — What is `CharacterBody2D`? | `CharacterBody2D` is a specialized **Node class** designed for player/NPC movement with built-in collision response, gravity handling, and `move_and_slide()` — essentially a "pre-built character controller plugin." |
