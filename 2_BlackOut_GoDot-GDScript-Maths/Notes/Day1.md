# Godot Setup Notes (Q&A Style)

### Doubt 1: Can we delete `res://` or `icon.svg`?
**Solution:**  
- `res://` is not a file — it’s Godot’s virtual path prefix pointing to your project root. You cannot delete it; it’s built into the engine.  
- `icon.svg` *is* a file — the default robot icon. You can safely delete or replace it. If deleted, update **Project → Project Settings → Application → Config → Icon** to avoid a missing icon warning.

---

### Doubt 2: What are FileSystem Dock, Node, Scene, and can we change the main scene?
**Solution:**  
- **FileSystem Dock** → Shows your project folder structure (`res://`). Works like a file explorer inside Godot.  
- **Node** → Smallest building block. Each Node has one job (e.g., `Sprite2D` shows an image, `CollisionShape2D` defines a hitbox).  
- **Scene** → A tree of Nodes saved as `.tscn`. Reusable and composable.  
- **Main Scene** → Yes, you can change it anytime. Set it in **Project → Project Settings → Application → Run → Main Scene**.

---

### Doubt 3: How do we set up the project scale?
**Solution:**  
- **Viewport Resolution** → Set in **Project Settings → Display → Window → Size**.  
  - Pixel-art style: 640×360.  
  - Higher-res art: 1280×720.  
  - Stretch Mode: `canvas_items`, Aspect: `keep`.  
- **World Unit (Tile Size)** → Common choice: 32×32 pixels.  
- **Character Scale** → Player ≈ 1.5 tiles tall (32×48px). Enemies/props sized consistently.

---

### Doubt 4: How should the folder structure look?
**Solution:**  
Organize inside Godot’s FileSystem Dock:

```
keep-ostriching/
├── project.godot
├── scenes/{main, player, enemies, ui, levels}
├── scripts/
├── assets/{art, audio, fonts}
└── resources/GDD.md
```

---

### Doubt 5: How do we create the Player scene?
**Solution:**  
1. Root Node → `CharacterBody2D` (rename to `Player`).  
2. Add `ColorRect` → size 32×48px → placeholder color.  
3. Add `CollisionShape2D` → `RectangleShape2D` → size 32×48px.  
4. Save as `res://scenes/player/Player.tscn`.  
5. Attach Script → `res://scripts/player.gd`.  
   ```gdscript
   extends CharacterBody2D

   const SPEED = 200.0

   func _physics_process(delta: float) -> void:
       var direction := Input.get_axis("ui_left", "ui_right")
       velocity.x = direction * SPEED
       move_and_slide()
   ```
6. Test with `F6` (current scene) or `F5` (main scene).

