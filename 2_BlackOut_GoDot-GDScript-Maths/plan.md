📂 BlackoutDevelopmentDocument(BDD)/
│
├── 1_Scope_Estimate.md
│   ├── Days Allocated (subset of total project days, BlackOut ≠ whole timeline)
│   ├── Definition of Done (NEW)
│   │   └── 3–5 testable sentences, e.g. "Player moves, enemy spawns, touch = game over, restart works, 60 FPS"
│   ├── Scene Count (Main, Player, Enemy, UI/HUD, GameOver)
│   ├── Per-Scene Breakdown
│   │   ├── Scripts (1 per behavior, not per node)
│   │   ├── Nodes (CharacterBody2D/Area2D + CollisionShape2D + ColorRect)
│   │   └── Assets (~0)
│   ├── Daily Milestones (NEW)
│   │   └── Day → one visible, runnable result (not "work on player")
│   ├── Risk List (NEW)
│   │   └── "What could eat my days?" + fallback (e.g. collisions misbehave → simplify to distance check)
│   └── Cut List (deferred to Art/Level phase, written down so you don't build early)
│
├── 2_Mechanics_Spec.md (NEW — the heart of the BDD)
│   └── One block per mechanic:
│       ├── Input → Rule → Output
│       ├── State diagram (see 3_State_Diagrams/)
│       ├── Tunable constants (speed, gravity, spawn_interval…)
│       └── Edge cases (what if two things happen the same frame?)
│
├── 3_State_Diagrams/ (NEW — pulled out of Scope_Estimate)
│   ├── player_states.md   (Idle → Move → Hurt → Dead)
│   ├── enemy_states.md
│   └── game_flow.md       (Start → Playing → Paused → GameOver → Restart)
│
├── 4_Math_Sheet.md (NEW — your 2D game math lives here)
│   ├── Scale: 1 world unit = ? px, player = ? px, arena = ? px
│   ├── Movement: velocity = direction.normalized() * speed
│   ├── Frame-independence: position += velocity * delta
│   ├── Spawning: random point on arena edge (formula)
│   ├── Difficulty curve: spawn_interval as a function of score
│   └── Any formula used in code, written here first
│
├── 5_Architecture.md (NEW)
│   ├── Scene Tree Diagram (who is parent/child of whom)
│   ├── Signals Map (who emits → who listens, e.g. player.died → game_manager)
│   ├── Autoloads (global singletons, if any, and why)
│   ├── Input Map (action names: move_left, move_right… keep player-agnostic)
│   └── Collision Layers & Masks Table (layer name → who is on it → who scans it)
│
├── 6_Folder_Structure.md
│   └── res://
│       ├── scenes/        (main, player, enemy, ui)
│       ├── scripts/       (player.gd, enemy.gd, game_manager.gd)
│       ├── autoload/      (NEW — globals)
│       ├── resources/     (NEW — constants, themes, later art/audio slots)
│       ├── docs/          (NEW — keep the BDD files inside the project)
│       │   └── cut_list.md  (replaces remaining.txt)
│       └── project.godot
│       Naming rules (NEW): snake_case files, PascalCase nodes, one script ↔ one purpose
│
├── 7_Project_Setup_Checklist.md
│   ├── Scale Definition (links to 4_Math_Sheet)
│   ├── Viewport & Window (resolution, stretch mode, aspect, scaling mode)
│   ├── Main Scene assignment
│   ├── Basic UI setup
│   ├── Physics layers named in Project Settings (NEW)
│   ├── Input map entries created (NEW)
│   └── Version control initialized + .gitignore (NEW)
│
├── 8_Test_Plan.md (NEW)
│   ├── Per-mechanic checks (each maps to a Definition of Done line)
│   ├── Break-it tests (go off-screen, die at the same moment as scoring, spam restart)
│   └── Performance check (FPS, enemy count at difficulty cap)
│
├── 9_Art_Handoff.md (NEW)
│   ├── Placeholder → real asset map (ColorRect "Player" → Sprite2D/AnimatedSprite2D)
│   ├── Hitbox rule: collision shapes stay the same, art adapts to them
│   └── Hooks left for animation/audio (signals, state changes that will trigger them)
│
└── README.md (Quick index + current status + "next action")