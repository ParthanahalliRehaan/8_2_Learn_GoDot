## 🔥 Godot 4 — Deep 2D Game Dev Roadmap (20 Days)
```
🎮 Godot 4 — Ultra-Deep 2D Game Dev Roadmap (20 Days)
│
├── 📘 Phase 1: Core Engine + GDScript Deep Dive (5 days)
│   │
│   ├── Day 1 — Nodes, Scenes & The Scene Tree
│   │   ├── What is a Node? (the building block)
│   │   ├── Scene composition: nodes as a tree
│   │   ├── Scene Tree: _ready(), _process(), _physics_process()
│   │   ├── Instancing scenes (PackedScene)
│   │   ├── Signals: loose coupling between nodes
│   │   ├── DEEP: Node communication patterns — call(), call_deferred(), set_deferred()
│   │   ├── DEEP: Memory management — reference counting, cyclic references
│   │   ├── DEEP: Node groups — dynamic group management, get_tree().call_group()
│   │   └── 📎 Docs: [Nodes & Scene Instances](https://docs.godotengine.org/en/stable/tutorials/scripting/nodes_and_scene_instances.html)
│   │
│   ├── Day 2 — GDScript OOP Deep Dive
│   │   ├── extends, class_name, static typing (→)
│   │   ├── _init() constructor, default args
│   │   ├── Inheritance: method overriding, super.method()
│   │   ├── Composition pattern: has-a vs is-a (Node-based)
│   │   ├── Inner classes (class inside class)
│   │   ├── @onready, @export, @export_enum, @export_range
│   │   ├── DEEP: Property system — @property getters/setters
│   │   ├── DEEP: Static variables & functions
│   │   ├── DEEP: Error handling — push_error(), push_warning(), assert()
│   │   └── 📎 Docs: [GDScript Basics](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html) | [Static Typing](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/static_typing.html)
│   │
│   ├── Day 3 — Signals, Callables & Loose Coupling
│   │   ├── connect(), disconnect(), emit()
│   │   ├── Callable.bind() for passing args
│   │   ├── await signal_name (coroutines)
│   │   ├── Signal bus pattern (Autoload)
│   │   ├── Groups: add_to_group(), get_tree().call_group()
│   │   ├── DEEP: Signal connection flags — CONNECT_DEFERRED, CONNECT_ONE_SHOT, CONNECT_PERSIST
│   │   ├── DEEP: Lambda signals — inline connections
│   │   ├── DEEP: Weak references — WeakRef to avoid memory leaks
│   │   └── 📎 Docs: [Signals](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html#signals)
│   │
│   ├── Day 4 — 2D Math & Transforms (CRITICAL)
│   │   ├── Vector2: x, y, length(), normalized(), dot(), cross()
│   │   ├── distance_to(), direction_to(), angle_to()
│   │   ├── lerp(), move_toward(), clamp()
│   │   ├── Transform2D: basis, origin, affine_inverse()
│   │   ├── look_at(), global_position vs position
│   │   ├── Rotation: radians, deg_to_rad(), rad_to_deg()
│   │   ├── DEEP: Matrix math basics — what Transform2D actually is (2x3 matrix)
│   │   ├── DEEP: Basis vectors — x and y basis meaning
│   │   ├── DEEP: Interpolation — smoothstep(), custom ease curves
│   │   └── 📎 Docs: [Vector2](https://docs.godotengine.org/en/stable/classes/class_vector2.html) | [Transform2D](https://docs.godotengine.org/en/stable/classes/class_transform2d.html)
│   │
│   └── Day 5 — Input Handling Deep Dive
│       ├── InputMap: actions, deadzone, strength
│       ├── InputEvent: _input(), _unhandled_input(), _gui_input()
│       ├── is_action_pressed() vs is_action_just_pressed() vs get_action_strength()
│       ├── Input buffering (queue last few inputs)
│       ├── Custom input states (coyote time, jump buffering)
│       ├── DEEP: Input event flow — exact order of _input() → _gui_input() → _unhandled_input()
│       ├── DEEP: Consuming events — get_viewport().set_input_as_handled()
│       ├── DEEP: Custom InputEvent — creating your own event types
│       └── 📎 Docs: [Input Handling](https://docs.godotengine.org/en/stable/tutorials/inputs/inputevent.html)
│
├── 📗 Phase 2: Physics & Movement Systems (5 days)
│   │
│   ├── Day 6 — Kinematic Movement (CharacterBody2D)
│   │   ├── move_and_slide() vs move_and_collide()
│   │   ├── Velocity: acceleration, friction, max_speed
│   │   ├── Gravity application, terminal velocity
│   │   ├── is_on_floor(), is_on_wall(), is_on_ceiling()
│   │   ├── Slope handling, floor_max_angle
│   │   ├── DEEP: Velocity decomposition — separating X and Y logic
│   │   ├── DEEP: Acceleration curves — linear vs exponential
│   │   ├── DEEP: Snap to floor — floor_snap_length, floor_constant_speed
│   │   └── 📎 Docs: [CharacterBody2D](https://docs.godotengine.org/en/stable/classes/class_characterbody2d.html) | [Using CharacterBody2D](https://docs.godotengine.org/en/stable/tutorials/physics/using_character_body_2d.html)
│   │
│   ├── Day 7 — Advanced 2D Physics Math
│   │   ├── Velocity vectors: adding forces (knockback, wind)
│   │   ├── Reflection: bounce off surfaces
│   │   ├── Projectile motion: launch angles, arcs
│   │   ├── Circular motion: orbit around point
│   │   ├── Steering behaviors: seek, flee, arrive
│   │   ├── DEEP: Force integration — Euler vs semi-implicit Euler
│   │   ├── DEEP: Impulse vs force — when to use apply_impulse() vs apply_force()
│   │   ├── DEEP: Spring physics — Hooke's law for platforms
│   │   └── 📎 Docs: [Physics Introduction](https://docs.godotengine.org/en/stable/tutorials/physics/physics_introduction.html)
│   │
│   ├── Day 8 — Collision Detection Deep Dive
│   │   ├── CollisionShape2D types: Rectangle, Circle, Capsule, Polygon
│   │   ├── Collision layers & masks (binary math)
│   │   ├── Area2D: body_entered, body_exited, area_entered
│   │   ├── RayCast2D: cast_to, is_colliding(), get_collision_point()
│   │   ├── ShapeCast2D (Godot 4) for sweep tests
│   │   ├── DEEP: Collision solver — how Godot resolves overlaps
│   │   ├── DEEP: CCD (Continuous Collision Detection) — continuous_cd for fast objects
│   │   ├── DEEP: One-way collision — one_way_collision tuning
│   │   └── 📎 Docs: [Physics Bodies](https://docs.godotengine.org/en/stable/tutorials/physics/physics_bodies.html) | [RayCast2D](https://docs.godotengine.org/en/stable/classes/class_raycast2d.html)
│   │
│   ├── Day 9 — RigidBody2D & Forces
│   │   ├── apply_force(), apply_impulse(), apply_torque()
│   │   ├── Mass, friction, bounce (restitution)
│   │   ├── Center of mass, inertia
│   │   ├── Sleeping bodies, can_sleep
│   │   ├── Integrating forces manually (_integrate_forces)
│   │   ├── DEEP: Custom integrators — _integrate_forces() deep dive
│   │   ├── DEEP: Center of mass offset — CENTER_OF_MASS_MODE_CUSTOM
│   │   ├── DEEP: Joint systems — PinJoint2D, DampedSpringJoint2D, GrooveJoint2D
│   │   └── 📎 Docs: [RigidBody2D](https://docs.godotengine.org/en/stable/classes/class_rigidbody2d.html)
│   │
│   └── Day 10 — Complex Movement Systems
│       ├── Platformer: coyote time, jump buffering, variable jump height
│       ├── Top-down: 8-directional, acceleration curves
│       ├── Dash: i-frames, cooldown, trail effect (simple Line2D)
│       ├── Wall jump, wall slide
│       ├── Moving platforms (kinematic + remote transform)
│       ├── DEEP: Variable jump height — cut gravity on release
│       ├── DEEP: Dash — hitbox activation frames, after-image effect
│       ├── DEEP: Moving platforms — RemoteTransform2D, physics-synced
│       └── 📎 Docs: [Platformer 2D](https://docs.godotengine.org/en/stable/tutorials/physics/kinematic_character_2d.html)
│
├── 📙 Phase 3: Game Systems & Architecture (5 days)
│   │
│   ├── Day 11 — State Machines
│   │   ├── Enum-based state machine
│   │   ├── Node-based state machine (State extends Node)
│   │   ├── Transitions: conditions, enter/exit callbacks
│   │   ├── Hierarchical states (sub-states)
│   │   ├── Animation state machine (even without art, logic holds)
│   │   ├── DEEP: Pushdown automata — state stack for "attack while running"
│   │   ├── DEEP: Hierarchical — nested state machines
│   │   ├── DEEP: Animation tree integration — AnimationNodeStateMachinePlayback
│   │   └── 📎 Docs: [AnimationTree](https://docs.godotengine.org/en/stable/tutorials/animation/animation_tree.html)
│   │
│   ├── Day 12 — Combat & Hit Systems
│   │   ├── Hitbox vs Hurtbox (Area2D layers)
│   │   ├── Damage calculation: attack, defense, multipliers
│   │   ├── Invincibility frames (timer + collision disable)
│   │   ├── Knockback vectors
│   │   ├── Health system: max_hp, current_hp, damage(), heal(), die()
│   │   ├── DEEP: Frame data — startup/active/recovery frames
│   │   ├── DEEP: Combo system — input window, chain validation, damage scaling
│   │   ├── DEEP: Hit pause — freezing game for impact frames
│   │   └── 📎 Docs: [Area2D](https://docs.godotengine.org/en/stable/classes/class_area2d.html)
│   │
│   ├── Day 13 — Spawning, Object Pooling & AI
│   │   ├── Object pooling: pre-instantiate, reset, reuse
│   │   ├── Spawners: timer-based, wave-based, trigger-based
│   │   ├── Simple AI: patrol, chase, attack (state machine)
│   │   ├── A* pathfinding: NavigationAgent2D, NavigationRegion2D
│   │   ├── Line of sight: RayCast2D checks
│   │   ├── DEEP: Pool architecture — ObjectPool singleton, Poolable interface
│   │   ├── DEEP: AI perception — vision cones, hearing radius, memory
│   │   ├── DEEP: Formation movement — leader-follower, flocking basics
│   │   └── 📎 Docs: [Navigation](https://docs.godotengine.org/en/stable/tutorials/navigation/navigation_introduction.html)
│   │
│   ├── Day 14 — A* Pathfinding & Navigation
│   │   ├── NavigationServer2D: low-level API for dynamic obstacles
│   │   ├── NavigationPolygon: baking from TileMapLayer
│   │   ├── NavigationAgent2D: get_next_path_position(), is_navigation_finished()
│   │   ├── Obstacle avoidance: avoidance_enabled, radius, time_horizon
│   │   ├── Dynamic updates: NavigationRegion2D at runtime
│   │   ├── DEEP: Off-mesh links — jumping between nav regions
│   │   ├── DEEP: Hierarchical pathfinding — multiple nav meshes
│   │   └── 📎 Docs: [NavigationServer2D](https://docs.godotengine.org/en/stable/classes/class_navigationserver2d.html)
│   │
│   └── Day 15 — Save/Load, Inventory & Data
│       ├── Custom Resources for data (weapons, items)
│       ├── JSON save: FileAccess, parse JSON
│       ├── Inventory: Array[Item] or Dictionary
│       ├── Equipment system: slots, stats
│       ├── Persistent data: Autoload singleton
│       ├── DEEP: Resource serialization — custom ResourceFormatSaver/Loader
│       ├── DEEP: Inventory architecture — GridInventory, SlotInventory
│       ├── DEEP: Loot tables — weighted random, rarity tiers
│       └── 📎 Docs: [Resources](https://docs.godotengine.org/en/stable/tutorials/scripting/resources.html) | [FileAccess](https://docs.godotengine.org/en/stable/classes/class_fileaccess.html)
│
├── 📕 Phase 4: Polish, Effects & Advanced Systems (3 days)
│   │
│   ├── Day 16 — Camera, Screenshake & Polish
│   │   ├── Camera2D: smoothing, limits, zoom
│   │   ├── Screenshake: random offset, decay over time
│   │   ├── Screen wrap (Asteroids style)
│   │   ├── Camera follow: lerp to target, lookahead
│   │   ├── Frame-independent movement (delta time)
│   │   ├── DEEP: Noise-based shake — FastNoiseLite integration
│   │   ├── DEEP: Trauma system — trauma decays, max_trauma caps
│   │   ├── DEEP: Camera rooms — CameraBoundary nodes that lerp between zones
│   │   └── 📎 Docs: [Camera2D](https://docs.godotengine.org/en/stable/classes/class_camera2d.html)
│   │
│   ├── Day 17 — Particles, Effects & Shaders
│   │   ├── CPUParticles2D (simple, reliable)
│   │   ├── GPUParticles2D (pretty, hardware dependent)
│   │   ├── Trail rendering: Line2D with gradient
│   │   ├── Screen-space effects: BackBufferCopy
│   │   ├── Shader basics: shader_type canvas_item, VERTEX manipulation
│   │   ├── DEEP: GPUParticles2D — particle shaders, custom process/start shaders
│   │   ├── DEEP: Flash/Hit effects — white flash shader, dissolve shader
│   │   └── 📎 Docs: [Particles](https://docs.godotengine.org/en/stable/tutorials/2d/particle_systems_2d.html) | [Shaders](https://docs.godotengine.org/en/stable/tutorials/shaders/shader_reference/shader_functions.html)
│   │
│   └── Day 18 — Audio Systems
│       ├── AudioStreamPlayer2D: positional sound
│       ├── Audio buses & effects (reverb, low-pass)
│       ├── Dynamic music: crossfading between tracks
│       ├── SFX layering: random pitch/variation
│       ├── DEEP: Audio buses — reverb, low-pass, high-pass, distortion, chorus
│       ├── DEEP: Stem mixing — muting individual instruments
│       ├── DEEP: Audio pools — limiting concurrent sounds
│       └── 📎 Docs: [Audio](https://docs.godotengine.org/en/stable/tutorials/audio/audio_buses.html)
│
└── 📓 Phase 5: Architecture & Production (2 days)
    │
    ├── Day 19 — Advanced Architecture
    │   ├── Dependency injection: service locator pattern
    │   ├── Event bus: typed events, EventBus singleton
    │   ├── Scene pooling: ScenePool for UI popups
    │   ├── Modular design: plugin architecture
    │   ├── Data-driven design: CSV/JSON to Resource pipeline
    │   ├── Localization: TranslationServer, tr() keys
    │   └── 📎 Docs: [Autoloads](https://docs.godotengine.org/en/stable/tutorials/scripting/singletons_autoloads.html) | [Plugins](https://docs.godotengine.org/en/stable/tutorials/plugins/editor/making_plugins.html)
    │
    └── Day 20 — Optimization & Export
        ├── Profiling: Performance singleton, monitor graphs
        ├── Memory: object count tracking, ObjectDB leak detection
        ├── Draw calls: RenderingServer metrics, batching optimization
        ├── Texture atlases: AtlasTexture, TexturePacker workflow
        ├── Export pipeline: custom export presets
        ├── Web export: WASM limitations
        ├── Mobile: touch input, safe areas, performance scaling
        └── 📎 Docs: [Performance](https://docs.godotengine.org/en/stable/tutorials/performance/index.html) | [Exporting](https://docs.godotengine.org/en/stable/tutorials/export/exporting_basics.html)
```

---

## 🎯 Build-Along Milestones

| After Phase | Build This |
|-------------|-----------|
| Phase 1 | Player with coyote time, jump buffering, variable jump height |
| Phase 2 | Full platformer: wall jump, dash, moving platforms, slope sliding |
| Phase 3 | Combat system: combos, hit pause, status effects, enemy AI with pathfinding |
| Phase 4 | Polished game: screen shake, particles, dynamic audio, shaders |
| Phase 5 | Production-ready: save system, settings menu, optimized, exportable |

---

## 📚 Essential Docs Hub

- [Godot 4.4 Official Docs](https://docs.godotengine.org/en/stable/)
- [GDScript Reference](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/index.html)
- [2D Physics](https://docs.godotengine.org/en/stable/tutorials/physics/physics_introduction.html)
- [Animation](https://docs.godotengine.org/en/stable/tutorials/animation/introduction.html)
- [Navigation](https://docs.godotengine.org/en/stable/tutorials/navigation/navigation_introduction.html)

## What learnt?

### Phase 1: Core Engine + GDScript (Days 1-5)

**Day 1 — Nodes, Scenes & Scene Tree**
- A scene with a player node hierarchy: `Player (CharacterBody2D) → Sprite2D → CollisionShape2D`
- Scene instancing: drag player scene into level scene
- Signal connections: button press → function call
- Node groups: add enemies to "enemies" group, damage all at once

**Day 2 — GDScript Deep Dive**
- A custom class: `class_name HealthComponent extends Node`
- Static typing everywhere: `var speed: float = 200.0`
- `@export` variables editable in inspector
- `@onready` to safely get node references
- Inner classes for data structures

**Day 3 — Signals & Loose Coupling**
- Player emits `health_changed` signal → UI updates health bar
- Signal bus autoload: `EventBus.player_died.emit()`
- `Callable.bind()` to pass arguments to connections
- `await` for coroutines: `await get_tree().create_timer(1.0).timeout`

**Day 4 — 2D Math & Transforms**
- Smooth follow: `position = lerp(position, target, 0.1)`
- Rotate toward mouse: `look_at(get_global_mouse_position())`
- Move toward target: `velocity = position.direction_to(target) * speed`
- Custom easing functions for UI animations

**Day 5 — Input Handling**
- Custom InputMap actions: "jump", "attack", "dash"
- Input buffering: press jump 100ms before landing → still jumps
- Coyote time: walk off ledge, can still jump for 5 frames
- Input replay system: record inputs, play back for debugging

---

### Phase 2: Physics & Movement (Days 6-10)

**Day 6 — Kinematic Movement**
- Full platformer physics: gravity, acceleration, friction
- `move_and_slide()` with slope handling
- `is_on_floor()`, `is_on_wall()`, `is_on_ceiling()` checks
- Snap to floor for consistent ground detection

**Day 7 — Advanced Physics Math**
- Knockback: `velocity += knockback_vector`
- Wind force: constant push in one direction
- Bounce off walls: `velocity = velocity.bounce(collision_normal)`
- Projectile arc: calculate launch angle for target
- Steering: enemy seeks player using `velocity += steering_force`

**Day 8 — Collision Detection**
- Different hit shapes: Rectangle (body), Circle (head), Capsule (full body)
- Collision layers: Player=1, Enemy=2, Ground=3, Collectible=4
- RayCast2D: enemy checks line of sight to player
- ShapeCast2D: sweep test for dash collision

**Day 9 — RigidBody2D**
- Physics-based boxes: push them around
- Explosion force: `apply_impulse()` to nearby bodies
- Custom center of mass: top-heavy objects tip over
- Joints: swinging pendulum, spring platforms

**Day 10 — Complex Movement**
- Variable jump height: hold jump = higher, tap = lower
- Dash: press shift → fast burst + after-image trail
- Wall jump: slide down wall, press away + jump
- Wall slide: reduced gravity while touching wall
- Moving platforms: stand on platform, move with it
- Rope swing: grab rope, physics-based swinging

---

### Phase 3: Game Systems (Days 11-15)

**Day 11 — State Machines**
- Player states: Idle → Run → Jump → Fall → Attack → Hurt → Die
- State transitions: can only jump from Idle/Run, not from Hurt
- Hierarchical: Grounded {Idle, Run, Crouch} / Airborne {Jump, Fall, WallSlide}
- Pushdown stack: Attack → return to previous state

**Day 12 — Combat & Hit Systems**
- Hitbox (your attack) + Hurtbox (enemy body) separation
- Frame data: attack has 3 startup frames, 5 active frames, 10 recovery frames
- Invincibility frames: flash sprite, disable hurtbox for 1 second after hit
- Knockback: hit enemy flies back with `velocity = hit_direction * knockback_force`
- Combo system: light attack → light attack → heavy attack (timed input window)
- Hit pause: freeze game for 3 frames on impact for "juice"

**Day 13 — Spawning, Pooling & AI**
- Object pool: pre-create 50 bullets, reuse instead of create/destroy
- Enemy spawner: wave-based, timer-based, trigger-based
- AI patrol: walk between two points, idle at each
- AI chase: if player in vision cone, chase until lost sight
- AI attack: when in range, play attack animation
- Flocking: group of enemies move as cohesive unit

**Day 14 — A* Pathfinding**
- NavigationRegion2D: bake walkable areas from TileMap
- NavigationAgent2D: enemy finds path around obstacles
- Dynamic obstacles: moving crates block path, nav mesh updates
- Off-mesh links: enemy knows it can jump across gaps

**Day 15 — Save/Load, Inventory & Data**
- Save game: JSON file with player position, health, inventory
- Load game: read file, restore everything
- Custom Resource: `ItemData` with name, icon, damage, rarity
- Inventory: Array of items, drag-drop between slots
- Equipment: weapon slot, armor slot → affects stats
- Loot table: enemy dies → weighted random drop (common 60%, rare 30%, legendary 10%)

---

### Phase 4: Polish & Effects (Days 16-18)

**Day 16 — Camera & Screenshake**
- Camera2D follows player with smoothing
- Trauma-based screenshake: big hit = big shake, decays over time
- Camera boundaries: room-based camera, smooth transition between rooms
- Screen wrap: go off left edge → appear on right (Asteroids style)

**Day 17 — Particles & Shaders**
- Jump dust: CPUParticles2D burst on landing
- Death explosion: GPUParticles2D with color fade
- Trail effect: Line2D follows player during dash
- White flash shader: sprite turns white for 1 frame on hit
- Dissolve shader: enemy fades away pixel by pixel on death

**Day 18 — Audio**
- Footsteps: different sound for wood, metal, grass surfaces
- Positional audio: enemy growl gets louder as they approach
- Dynamic music: calm music → combat music when enemy near
- Audio bus: reverb in caves, muffled underwater

---

### Phase 5: Architecture & Production (Days 19-20)

**Day 19 — Advanced Architecture**
- Autoload singletons: GameManager, AudioManager, SaveManager, EventBus
- Event-driven: `EventBus.enemy_died.emit(position, enemy_type)`
- Dependency injection: `GameManager.get_player()` instead of hard paths
- Data-driven: enemy stats from CSV file, not hardcoded

**Day 20 — Optimization & Export**
- Texture atlas: all sprites in one image file
- Object pooling: bullets, enemies, damage numbers
- `set_process(false)` on off-screen enemies
- Export to Windows .exe, Web HTML5, Linux, Android
- `.gitignore`: ignore `.godot/` folder, `*.tmp` files

---

### 🎯 Final Game: What You'll Have

```
📁 YourGame/
├── Player (animated sprite, state machine, health, inventory)
│   ├── Moves: walk, run, jump, double jump, wall jump, dash
│   ├── Attacks: light combo, heavy attack, special
│   └── Takes damage: i-frames, knockback, death + respawn
│
├── Enemies (3+ types with different AI)
│   ├── Patroller: walks back and forth
│   ├── Chaser: sees player, pathfinds around obstacles
│   └── Flying: A* pathfinding in air
│
├── Level (TileMap with collision)
│   ├── Platforms: static, moving, crumbling
│   ├── Hazards: spikes, lava (instant kill)
│   └── Collectibles: coins, health potions, keys
│
├── UI (CanvasLayer)
│   ├── Health bar (depletes with damage, lerp animation)
│   ├── Mana/ stamina bar (for dash/special)
│   ├── Inventory grid (drag-drop items)
│   ├── Pause menu (resume, save, quit)
│   └── Death screen (retry, load last save)
│
├── Systems (Autoload singletons)
│   ├── GameManager: score, level progression
│   ├── AudioManager: music layers, SFX pools
│   ├── SaveManager: JSON save/load, multiple slots
│   └── EventBus: decoupled communication
│
└── Polish
    ├── Screenshake on heavy hits
    ├── Particle bursts (jump, land, hit, die)
    ├── Shader effects (flash white, dissolve, glow)
    ├── Camera rooms + smooth transitions
    └── Full audio: music, footsteps, combat SFX
```

---

### 🎮 Playable Features

| Feature | What It Does |
|---------|-------------|
| Move | WASD / Arrow keys |
| Jump | Space (variable height) |
| Double Jump | Space again in air |
| Wall Jump | Near wall + away + jump |
| Wall Slide | Hold toward wall in air |
| Dash | Shift (invincible, fast, trail) |
| Attack | Z / J (3-hit combo) |
| Heavy Attack | Hold attack (slower, more damage) |
| Take Damage | Enemy touches you → knockback, i-frames |
| Die | Health 0 → fade out, respawn at checkpoint |
| Collect | Walk over coin → +score, particle burst |
| Open Inventory | Tab / I → pause game, manage items |
| Equip Item | Drag to weapon/armor slot |
| Save Game | Pause menu → Save to slot 1/2/3 |
| Load Game | Title screen → Continue |

