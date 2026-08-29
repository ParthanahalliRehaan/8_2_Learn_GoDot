## GODOT 4.x + GDSCRIPT + CODE ART + ANIMATION + MATH — MASTER INDEX
### 30 DAYS | 2D ONLY

**Legend:**
- **[G]** = Godot Engine concept
- **[S]** = GDScript programming
- **[A]** = Code Art technique
- **[N]** = Animation principle
- **[M]** = Math concept

---

### PHASE 1: Core Foundations (Days 1–6)

```
── Day 1 — Scene Tree & Canvas Drawing
    [G] Scene Tree: parent-child hierarchy, transforms, global vs local
    [G] Node types: Node2D, Control, CanvasItem
    [G] Scenes as reusable components (.tscn)
    [G] Instancing: instantiate(), add_child(), queue_free()
    [G] Node paths: $NodeName, %UniqueName, get_node()
    [S] _draw() override method
    [S] Class inheritance: extends Node2D
    [A] draw_rect(), draw_circle(), draw_line(), draw_polygon()
    [A] CanvasItem: modulate, self_modulate, z_index
    [A] queue_redraw() for updating visuals
    [A] Color creation: Color.hex(), Color.html()
    [M] Cartesian coordinates (x, y positioning)
    [M] Nested for loops for grid generation
    [M] Random selection from arrays
    └── SKIP: Custom nodes, Editor plugins, GDExtension

── Day 2 — GDScript + Generative Shapes
    [S] Variables, functions, loops, conditionals
    [S] Static typing: var, :=, -> ReturnType
    [S] @export vs @onready
    [S] Arrays, dictionaries, match statements
    [S] String formatting
    [A] Procedural shape generation
    [A] Pattern creation: grids, spirals, waves
    [A] Random seed control for reproducibility
    [A] Color palette systems
    [M] Random number generation (uniform distribution)
    [M] Range mapping: converting random values to sizes
    [M] Probability: weighted random selection
    [M] Nested iteration for pattern generation
    └── SKIP: Bytecode internals, C# interop

── Day 3 — Signals + Animated Code Art
    [G] signal keyword, emit_signal(), .connect()
    [G] Built-in signals: _ready, _process, _physics_process
    [G] await keyword for async operations
    [S] Decoupled communication between nodes
    [S] Timer-based callbacks
    [A] _process(delta) for continuous animation
    [A] Sine wave oscillation for pulsing effects
    [A] Phase offsets for varied motion
    [N] Timing: frame duration, loop points
    [N] Easing: smooth vs linear motion
    [M] Sine function: sin(time), amplitude, frequency
    [M] Phase shift: offsetting wave starts
    [M] Delta time: frame-independent animation
    [M] Periodic functions: repeating patterns
    └── SKIP: Physics-based animation, spring systems

── Day 4 — Input + Interactive Code Art
    [G] Input Map configuration
    [G] Input.get_vector(), is_action_pressed()
    [G] CharacterBody2D, move_and_slide()
    [G] CollisionShape2D, Area2D, body_entered signals
    [S] Mouse position tracking
    [S] Array-based trail systems
    [A] Distance-based visual effects
    [A] Click ripple expansion
    [A] Velocity-based color shifting
    [A] Particle-like behavior from code
    [N] Trail rendering: position history
    [N] Interactive response timing
    [M] Euclidean distance: sqrt(dx² + dy²)
    [M] Vector math: direction, magnitude
    [M] Ring buffer (circular array) for trails
    [M] Force simulation: attraction/repulsion
    [M] Velocity calculation: position derivative
    └── SKIP: RigidBody physics, multi-touch

── Day 5 — TileMap + Procedural Generation
    [G] TileMapLayer, TileSet, atlas tiles
    [G] Terrain painting, auto-tiling
    [G] Physics/navigation layers on tiles
    [S] Noise-based generation logic
    [S] Cellular automata rules
    [A] Color-coded biome visualization
    [A] Procedural tile decoration
    [N] Smooth transitions between tile types
    [M] Simplex/Perlin noise (FastNoiseLite)
    [M] Cellular automata: neighbor counting
    [M] Thresholding: noise → discrete tiles
    [M] Room-and-corridor: graph theory basics
    [M] Flood fill for connected regions
    └── SKIP: Custom tile shaders, infinite worlds

── Day 6 — Resources + Art Data Systems
    [G] Custom Resources (.tres)
    [G] Autoloads (Singletons)
    [G] FileAccess, JSON serialization
    [S] Resource class extension
    [S] Data-driven design patterns
    [A] Palette as Resource
    [A] Theme swapping at runtime
    [A] Seed-based reproducible art
    [N] Consistent animation across themes
    [M] Seed-based random: seeded RNG
    [M] JSON serialization of art data
    [M] Resource reference management
    └── SKIP: ResourceFormatLoader, binary files
```

---

### PHASE 2: Intermediate Patterns (Days 7–12)

```
── Day 7 — Tweens + Code-Driven Motion
    [G] AnimationPlayer keyframes
    [G] Tween system: create_tween()
    [G] GPUParticles2D basics
    [S] tween_property(), tween_callback()
    [S] Chaining: .chain().tween_property()
    [A] Shape morphing via tween
    [A] Staggered animations
    [A] Easing visualization
    [N] Easing curves: bounce, elastic, back
    [N] Stagger: delay between elements
    [N] Morphing: shape interpolation
    [M] Interpolation: lerp(a, b, t)
    [M] Easing functions: linear, quad, cubic, bounce
    [M] Bezier curves (implicit in tweens)
    [M] Time mapping: duration → progress
    └── SKIP: AnimationTree, shader particles

── Day 8 — UI + Code-Generated Interfaces
    [G] Control nodes: Button, Label, Panel
    [G] Layouts: HBox, VBox, Margin, Grid
    [G] Anchors, margins, responsive design
    [S] Custom Control with _draw()
    [S] get_drag_data(), drop_data()
    [A] Code-drawn buttons, progress bars
    [A] Health bars with damage preview
    [A] Dynamic borders, pulsing glow
    [A] Icon generation from shapes
    [N] UI transitions: hover, press, release
    [N] Progress bar fill animation
    [M] Rectangle intersection: hover detection
    [M] Percentage mapping: value → fill width
    [M] Gradient creation: color interpolation
    [M] Layout math: container sizing
    └── SKIP: Shader UI, complex themes

── Day 9 — Scene Management + Transitions
    [G] change_scene_to_file(), change_scene_to_packed()
    [G] ResourceLoader, loading screens
    [G] get_tree().paused, process_mode
    [S] Menu stack implementation
    [S] Screenshot capture
    [A] Transition effects via _draw()
    [A] Circle wipe, fade, pixel dissolve
    [A] Loading bar animation
    [A] Parallax background transitions
    [N] Transition timing: duration, overlap
    [N] Loading progress visualization
    [M] Circle geometry: radius expansion
    [M] Pixel grid manipulation
    [M] Alpha blending: fade calculations
    [M] Viewport coordinate mapping
    └── SKIP: Multi-threaded loading

── Day 10 — Audio + Reactive Visuals
    [G] AudioStreamPlayer, Audio buses
    [G] AudioEffect: reverb, low-pass
    [G] Camera2D offset for screen shake
    [G] Engine.time_scale for hitstop
    [S] SpectrumAnalyzer usage
    [S] Beat detection logic
    [A] Audio-reactive shapes
    [A] Frequency bar visualizer
    [A] Beat-synchronized pulsing
    [N] Audio-visual sync
    [N] Reactive motion timing
    [M] FFT basics: frequency → amplitude
    [M] Threshold detection: peak finding
    [M] BPM → seconds conversion
    [M] Audio spectrum binning
    [M] Decay functions for smooth response
    └── SKIP: Procedural audio, 3D audio

── Day 11 — State Machines + Visual States
    [G] State pattern: enum + match
    [G] Finite State Machine classes
    [G] Scene composition
    [S] State transitions, entry/exit callbacks
    [S] Visual debug drawing
    [A] State-based color coding
    [A] Visual tells: glow, pulse, shake
    [A] Debug state visualization
    [N] State transition animations
    [N] Visual feedback for state changes
    [M] State machine graph theory
    [M] Transition probability
    [M] Hysteresis: preventing flicker
    └── SKIP: Behavior trees, ECS

── Day 12 — AI + Procedural Enemies
    [G] NavigationAgent2D, NavigationRegion2D
    [G] RayCast2D for line of sight
    [G] Pathfinding integration
    [S] Patrol waypoints, chase logic
    [S] Distance checks, vision cones
    [A] Procedural polygon enemies
    [A] Visual tells: warning glow
    [A] Path line visualization
    [A] Vision cone drawing
    [N] Enemy animation states
    [N] Anticipation: telegraphing attacks
    [M] Polygon generation: n-sided shapes
    [M] Ray casting: line-circle intersection
    [M] Angle calculations: vision cone
    [M] Path smoothing: Bresenham, A*
    [M] Distance metrics: Manhattan, Euclidean
    └── SKIP: Flocking, GOAP
```

---

### PHASE 3: Advanced Patterns (Days 13–18)

```
── Day 13 — Inventory + Code-Generated Items
    [G] Item Resources
    [G] GridContainer, drag and drop
    [S] Inventory array management
    [S] Tooltip system
    [A] Procedural item icons
    [A] Rarity visualization: glow, pulse
    [A] Stat bar comparison
    [A] Slot highlight animations
    [N] Drag animation: scale, opacity
    [N] Drop feedback: bounce, flash
    [M] Shape composition: combining primitives
    [M] Glow intensity: exponential mapping
    [M] Grid coordinate math
    [M] Stack size calculations
    └── SKIP: Crafting, multiplayer sync

── Day 14 — Shaders + Advanced Effects
    [G] ShaderMaterial on CanvasItem
    [G] Vertex vs fragment shaders
    [G] TIME uniform
    [S] Shader parameter passing
    [S] Material instancing
    [A] Glow shader
    [A] Dissolve effect
    [A] Pixelate transition
    [A] Chromatic aberration
    [N] Shader-driven animation
    [N] Effect timing: trigger, duration, fade
    [M] UV coordinates: 0-1 mapping
    [M] Noise functions: simplex, perlin
    [M] Distance fields: glow calculation
    [M] Color space: RGB, HSV manipulation
    [M] Matrix transformations: vertex shader
    └── SKIP: 3D shaders, compute, raymarching

── Day 15 — Debugging + Visual Debugging
    [G] Remote Scene Tree, breakpoints
    [G] Performance Monitor
    [G] Object pooling
    [S] print(), push_warning(), push_error()
    [S] Debug overlay system
    [A] FPS counter drawing
    [A] Collision visualization
    [A] AI state debug drawing
    [A] Frame time graph
    [N] Real-time metric visualization
    [M] Statistics: mean, min, max FPS
    [M] Rolling average: frame time smoothing
    [M] Graph scaling: auto-range
    [M] Object count tracking
    └── SKIP: C++ profiling

── Day 16 — Project Organization
    [G] Folder structure best practices
    [G] .gitignore for Godot
    [S] Script ordering conventions
    [S] Art utility functions
    [A] Reusable draw modules
    [A] Palette resource organization
    [A] Effect preset system
    [N] Consistent animation standards
    [M] Modular arithmetic for organization
    [M] Naming convention patterns
    └── SKIP: CI/CD, advanced Git

── Day 17 — Testing + Export
    [G] GUT framework
    [G] Export presets: Web, Windows
    [G] Command-line export
    [S] Unit tests for art functions
    [S] Performance assertions
    [A] Visual test verification
    [A] Cross-platform color checks
    [N] Animation timing tests
    [M] Assertion logic: expected vs actual
    [M] Performance benchmarks
    [M] Statistical testing
    └── SKIP: Console ports

── Day 18 — Dialogue + Narrative
    [G] JSON dialogue parsing
    [G] Typing effect implementation
    [S] Choice branching logic
    [S] Quest state management
    [A] Code-generated portraits
    [A] Emotion-based shape morphing
    [A] Text box animations
    [A] Choice button effects
    [N] Typing speed: chars per second
    [N] Portrait expression transitions
    [M] String manipulation: substring, length
    [M] Timer-based character reveal
    [M] Branching probability
    └── SKIP: Ink integration
```

---

### PHASE 4: Polish & Juice (Days 19–22)

```
── Day 19 — Save Systems
    [G] FileAccess: user:// directory
    [G] JSON serialization
    [S] Save data structure design
    [S] Multiple save slots
    [A] Save thumbnail generation
    [A] Save slot card UI
    [A] Procedural world seed saving
    [N] Save animation: spinner, progress
    [M] Seed-based regeneration: deterministic
    [M] Screenshot capture: viewport buffer
    [M] Data compression: JSON minification
    [M] Encryption: XOR, simple crypto
    └── SKIP: Cloud saves, Steamworks

── Day 20 — Game Feel
    [G] Camera2D: smoothing, limits
    [G] Engine.time_scale
    [S] Screen shake implementation
    [S] Hitstop logic
    [A] Impact frame flash
    [A] Damage number floating
    [A] Particle burst from _draw()
    [A] Chromatic aberration on damage
    [N] Screen shake: amplitude, frequency, decay
    [N] Hitstop: duration, recovery
    [N] Impact anticipation
    [M] Random offset: shake generation
    [M] Exponential decay: shake damping
    [M] Time dilation: scale factor math
    [M] Parabolic arc: damage number float
    [M] Particle physics: velocity, gravity, drag
    └── SKIP: Complex simulations

── Day 21 — Settings + Options
    [G] AudioServer volume control
    [G] DisplayServer window modes
    [G] InputMap rebinding
    [S] ConfigFile for persistence
    [S] Settings menu implementation
    [A] Code-drawn sliders
    [A] Toggle button visuals
    [A] Key binding display
    [A] Audio waveform visualization
    [N] Slider drag feedback
    [N] Toggle switch animation
    [M] Linear mapping: slider → value
    [M] Logarithmic mapping: audio dB
    [M] Resolution aspect ratios
    [M] Input event detection
    └── SKIP: Advanced graphics settings

── Day 22 — Title Screen + Credits
    [G] Scene transitions
    [G] Export final builds
    [S] Menu state machine
    [S] Credits scroll logic
    [A] Animated title assembly
    [A] Procedural background patterns
    [A] Morphing menu buttons
    [A] Decorative divider drawing
    [N] Title reveal: stagger, scale
    [N] Background ambient motion
    [N] Button hover anticipation
    [M] Text positioning: centering, alignment
    [M] Scroll speed: pixels per second
    [M] Pattern generation: flowing lines
    [M] Easing: title entrance curves
    └── SKIP: Cinematic intros
```

---

### PHASE 5: Genre Systems (Days 23–27)

```
── Day 23 — Rhythm Game Foundations
    [G] AudioStreamGenerator
    [G] Playback position reading
    [S] Conductor pattern
    [S] Input timing windows
    [A] Lane backgrounds
    [A] Note shapes: circles, diamonds
    [A] Hit feedback rings
    [A] Lane pulse on beat
    [N] Note spawn timing
    [N] Hit feedback duration
    [M] BPM → seconds per beat: 60/BPM
    [M] Timing windows: ±ms thresholds
    [M] Audio position tracking
    [M] Lane coordinate system
    [M] Note fall speed: pixels per second
    └── SKIP: FFT analysis, procedural music

── Day 24 — Rhythm Game Polish
    [G] Scoring system
    [G] JSON beatmap parsing
    [S] Combo multipliers
    [S] Accuracy grading
    [A] Score bar fill
    [A] Grade badge shapes
    [A] Procedural album art
    [A] Results screen animation
    [N] Combo counter bounce
    [N] Accuracy text flash
    [M] Multiplicative scoring
    [M] Grade thresholds: percentile
    [M] Scroll speed calibration
    [M] Pattern seeding: album art
    └── SKIP: Online leaderboards

── Day 25 — Fighting Game Core
    [G] Frame data Resources
    [G] Hitbox/hurtbox layers
    [G] State machine: 7 states
    [S] Input buffering: queue
    [S] Cancel windows
    [A] Stick-figure fighters
    [A] Attack trail lines
    [A] Impact star burst
    [A] Block shield hexagon
    [N] Frame-perfect timing
    [N] Anticipation: windup frames
    [M] Frame counting: 60fps logic
    [M] Buffer queue: FIFO array
    [M] Motion vectors: attack direction
    [M] Collision response: knockback
    [M] State transition matrix
    └── SKIP: Netcode rollback

── Day 26 — Fighting Game Advanced
    [G] Combo system
    [G] Super meter
    [S] Juggle gravity
    [S] Command input parsing
    [A] Super move flash
    [A] Rainbow trail effect
    [A] Combo counter burn
    [A] Parallax city background
    [N] Super cinematic timing
    [N] Combo decay: hitstun falloff
    [M] Damage scaling: diminishing returns
    [M] Juggle physics: gravity, velocity
    [M] Quarter-circle detection: angle sequence
    [M] Parallax: depth-based speed
    [M] Screen flash: whiteout duration
    └── SKIP: AI opponent

── Day 27 — Genre Mashup: Rhythm + Fighting
    [G] Hybrid input system
    [G] Dual meter management
    [S] Beat-attack sync logic
    [S] Rhythm super activation
    [A] Beat indicator circle
    [A] Perfect timing gold flash
    [A] Missed beat gray-out
    [A] Cyberpunk neon palette
    [N] Beat-attack anticipation
    [N] Rhythm bar fill animation
    [M] Beat alignment: modulo timing
    [M] Bonus damage: multiplier math
    [M] Rhythm meter: accuracy accumulation
    [M] Neon glow: additive color mixing
    └── SKIP: Multiplayer
```

---

### PHASE 6: Ship (Days 28–30)

```
── Day 28 — Integration
    [G] System integration
    [G] Performance optimization
    [S] Bug fixing patterns
    [S] Edge case handling
    [A] Visual style unification
    [A] Loading screen animation
    [N] Consistent juice across screens
    [M] Profiling: bottleneck identification
    [M] Complexity analysis: O(n) checks
    └── SKIP: New features

── Day 29 — Export + Polish
    [G] Export to Web, Windows
    [G] Build metadata
    [S] Final bug fixes
    [S] Performance: 60 FPS target
    [A] Final color grade
    [A] Icon generation
    [A] Screenshot capture
    [N] Trailer timing
    [M] Performance testing: stress tests
    [M] File size optimization
    [M] Web compatibility checks
    └── SKIP: Console, mobile

── Day 30 — SHIP IT
    [G] itch.io upload
    [G] Community sharing
    [S] Feedback collection
    [S] v1.1 planning
    [A] Portfolio documentation
    [A] Style guide template
    [N] Showcase reel editing
    [M] Analytics: playtime, completion
    [M] Feedback categorization
    └── SKIP: Marketing campaigns
```

---

## Math Coverage Summary

| Area | Concepts Learned |
|------|-----------------|
| **Algebra** | Coordinates, linear equations, percentages, ratios |
| **Trigonometry** | Sine/cosine, tangent, polar coords, angle wrapping |
| **Geometry** | Points, lines, polygons, distance, intersection, area |
| **Calculus (discrete)** | Derivatives (velocity), integration, rates of change |
| **Probability** | Random numbers, distributions, weighted selection |
| **Statistics** | Mean, min, max, rolling averages, percentiles |
| **Linear Algebra** | Vectors (2D), dot product, matrix transforms (shaders) |
| **Algorithms** | Sorting, searching, graphs, cellular automata, noise |

## What I can build?

| Genre               | Example Games                 | Status  |
| ------------------- | ----------------------------- | ------- |
| Platformer          | Celeste, Hollow Knight        | ✅      |
| Top-down action     | Zelda, Enter the Gungeon      | ✅      |
| Roguelike           | Binding of Isaac, Dome Keeper | ✅      |
| Metroidvania        | Ori, Guacamelee               | ✅      |
| Puzzle              | Tetris, Baba Is You           | ✅      |
| Tower Defense       | Plants vs Zombies             | ✅      |
| Bullet Hell         | Vampire Survivors             | ✅      |
| Rhythm              | Osu!, Friday Night Funkin'    | ✅      |
| Fighting            | TowerFall, Street Fighter 2   | ✅      |
| Visual Novel        | Doki Doki, Ace Attorney       | ✅      |
| Simulation          | Stardew Valley-lite           | ✅      |
| Card Game           | Slay the Spire, Inscryption   | ✅      |
| Turn-based Strategy | Into the Breach, XCOM 2D      | ✅      |

