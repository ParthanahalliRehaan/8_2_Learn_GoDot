# What should be the folder structure of 2D Action-Adventure Game?
Designing a clean folder structure for a 2D game in Godot is crucial for scalability. Here's a practical, battle-tested approach:

## Recommended Folder Structure

```
res://
├── addons/                    # Third-party plugins
├── assets/                    # Raw game assets (never edit directly)
│   ├── audio/
│   ├── fonts/
│   ├── images/
│   ├── tilesets/
│   └── shaders/
├── autoload/                  # Singletons (global scripts)
│   ├── game_manager.gd
│   └── audio_manager.gd
├── resources/                 # Custom Resource definitions
│   ├── item_data.gd
│   └── enemy_stats.gd
├── scenes/                    # All .tscn files
│   ├── characters/
│   │   ├── player/
│   │   │   ├── player.tscn
│   │   │   └── player.gd
│   │   └── enemies/
│   │       ├── slime.tscn
│   │       └── slime.gd
│   ├── levels/
│   │   ├── level_1.tscn
│   ├── ui/
│   │   ├── hud.tscn
│   │   ├── main_menu.tscn
│   │   └── pause_menu.tscn
│   └── props/
│       ├── coin.tscn
│       └── platform.tscn
├── scripts/                   # Shared/reusable scripts
│   ├── state_machine.gd
│   └── utils.gd
├── tests/                     # Unit/integration tests
└── project.godot
```

## Key Principles

1. **Co-locate scenes and scripts**: Keep `.gd` files next to their `.tscn` files (Godot makes this easy with "Make Unique" and relative paths). This prevents hunting across folders.

2. **Separate `assets/` from `scenes/`**: `assets/` holds source files (PNG, WAV) that you might re-export; `scenes/` holds the actual game objects that reference them.

3. **Use `autoload/` sparingly**: Only true globals (save system, audio, scene transition). Too many singletons create hidden dependencies.

4. **Group by function, not type**: `scenes/characters/player/` is better than dumping all scripts in `scripts/` and all scenes in `scenes/`.

## Scene Organization Tips

- **Composition over inheritance**: Use nodes as building blocks. A player scene might contain:
  ```
  Player (CharacterBody2D)
  ├── Sprite2D
  ├── CollisionShape2D
  ├── StateMachine (Node)
  ├── Hitbox (Area2D)
  └── Camera2D
  ```

- **Use `class_name` for shared logic**:
  ```gdscript
  # scripts/entity.gd
  class_name Entity
  extends CharacterBody2D
  
  @export var health: int = 100
  ```

- **Signals for decoupling**: Avoid direct node references across scenes. Use signals or the Event Bus pattern via an autoload.

## Common Mistakes to Avoid

| Bad Practice | Better Alternative |
|---|---|
| Flat structure with 50+ files in root | Nest logically by feature |
| Absolute paths in `get_node()` | Unique names (`%NodeName`) or exported NodePaths |
| Mixing WIP assets with final assets | `assets/_wip/` subfolder or separate project |
| Giant scripts with 500+ lines | Break into smaller scripts or state machines |

## Quick Start Template

If you want a minimal viable structure for a platformer:

```
res://
├── autoload/
│   └── game.gd
├── assets/
│   ├── sprites/
│   └── sfx/
├── scenes/
│   ├── player/
│   │   ├── player.tscn
│   │   └── player.gd
│   ├── enemies/
│   ├── levels/
│   └── ui/
└── project.godot
```

# What should be the folder structure of GDD?, Mentioned in Jargons/day0!