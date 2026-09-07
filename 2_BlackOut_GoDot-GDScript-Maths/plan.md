📂 BlackoutDevelopmentDocument(BDD)/
│
├── Scope_Estimate.md
│   ├── Days Allocated (subset of total project days — Blackout ≠ whole timeline)
│   ├── No. of Scenes (Main, Player, Enemy, UI/HUD, GameOver — count only what BlackOut needs)
│   ├── Per-Scene Breakdown
│   │   ├── Scripts (1 script per behavior, not per node)
│   │   ├── Nodes (CharacterBody2D/Area2D + Collision + ColorRect, nothing decorative)
│   │   └── Assets (should be ~0 — BlackOut uses ColorRect/Polygon2D, not sprites)
│   └── Cut List (mechanics from GDD explicitly deferred to Art/Level phase — write these down so you don't "accidentally" build them early)
│
├── Folder_Structure.md
│   └── res://
│       ├── scenes/
│       │   ├── main.tscn
│       │   ├── player.tscn
│       │   ├── enemy.tscn
│       │   └── ui.tscn
│       ├── scripts/
│       │   ├── player.gd
│       │   ├── enemy.gd
│       │   └── game_manager.gd
│       ├── remaining.txt
│       └── project.godot
│
└── Project_Setup.md
    ├── Scale Definition (world units → pixels, player size in px, arena bounds)
    ├── Viewport & Window (resolution, stretch mode, aspect)
    ├── Main Scene assignment (Project Settings → Application → Run → Main Scene)
    └── Physics/Input map basics needed for BlackOut only