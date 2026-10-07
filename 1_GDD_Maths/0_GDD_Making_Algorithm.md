## The GDD Algorithm (2D)

> Answer in order. Each answer limits the next one.

```
STEP 1  MODE         Single-player or multiplayer?
STEP 2  PLATFORM     Mobile / PC / Web / Console
STEP 3  DIMENSION    2D (fixed for this course)
STEP 4  PERSPECTIVE  Side / Top-down / Isometric (3/4)
STEP 5  GENRE        One primary genre (+ at most one hybrid)
STEP 6  CORE LOOP    One sentence: action → feedback → reward → repeat
STEP 7  MECHANICS    Pick from your perspective's menu (max 3 for v1)
STEP 8  MATH         Look up the math each mechanic needs
STEP 9  SCOPE CHECK  Can one person finish this?, Will this GAME be TOO decorative?, 
                     Yes, Don't proceed. Don't abandon the core idea. Just cut mechanics!
                     No, Proceed to the next step.
STEP 10 GDD          Implement the GDD in a well‑structured format (refer to the 2D GDD structure below)
```

---

## 2D GDD Structure/

> Follow the format below during GDD implementation.

```
└── 📂 GameDesignDocument(GDD)/
│   ├── Game_Overview.md
│   │   ├── Name & Title
│   │   ├── Description (linked to 📂 Genre_Hierarchy/)
│   │   ├── Genre, Game Type & Perspective (linked to 📂 Game_Perspectives/)
│   │   ├── Platform (Mobile, PC, Console)
│   │   ├── Player Mode (Singleplayer, Multiplayer, Co-op)
│   │   ├── Target Audience (Age group, region, casual vs hardcore)
│   │   ├── Monetization Strategy (Free-to-play, Premium, Ads, DLC)
│   │   └── Unique Selling Points (Check: Too Big? Too Decorative? → Stop if yes)
│   │
│   ├── Enhanced_GO.md
│   │   ├── Primitive Game Mechanics (Movement, Combat, Interaction)
│   │   ├── Compound Game Mechanics (Crafting, Progression, Puzzle systems)
│   │   ├── Object Lists
│   │   │   ├── Characters (Playable, NPCs, Bosses)
│   │   │   ├── Props (Collectibles, Obstacles, Environment items)
│   │   │   └── Interactive Objects (Doors, Switches, Traps)
│   │   ├── World Map
│   │   │   ├── Level Layouts
│   │   │   ├── Regions & Zones
│   │   │   └── Progression Flow (linear, branching, open-world)
│   │   ├── Game Menus (varies by game mode)
│   │   │   ├── Start Menu
│   │   │   ├── Main Menu
│   │   │   ├── Pause Menu
│   │   │   └── End Menu 
│   │   └── UI/UX Notes (HUD, Inventory, Dialogue boxes)
│   │
│   ├── Technical_Specs.md
│   │   ├── Engine & Framework (Unity, Godot, etc.)
│   │   ├── Resolution & Aspect Ratios
│   │   ├── Performance Targets (FPS, memory usage)
│   │   ├── Tool used for Art, Animation, Music
│   │   └── Input Methods (Keyboard, Controller, Touch)
│   │
│   └── README.md
!      └── Quick Index of all sections

```

---

### 📂 Game_Perspectives/

```
📂 Perspectives/
│
├── 📂 Side_View (Platformer view)
│   ├── Camera       Looking at the world from the side, horizontal plane
│   ├── Gravity      YES
│   ├── Movement     Left/right + jump
│   ├── Strengths    Intuitive controls, strong character action, classic appeal
│   ├── Challenges   Mostly linear, level design must stay engaging, less immersion
│   ├── Best genres  Action, Platformer, Metroidvania, Puzzle
│   └── Godot start  CharacterBody2D + move_and_slide(), gravity added to velocity.y
│
├── 📂 Top_Down
│   ├── Camera       Directly overhead, bird's-eye
│   ├── Gravity      NO
│   ├── Movement     8-directional, sprint/dash
│   ├── Strengths    Simple navigation, clear view of the environment
│   ├── Challenges   Little sense of depth, can feel flat
│   ├── Best genres  RPG, Roguelike, Shooter, Strategy, Simulation
│   ├── Godot start  CharacterBody2D, Input.get_vector(), gravity = 0
│   │
│   ├── 📂 Three_Quarter (3/4 view)
│   │   ├── Camera       Top-down, but sprites are drawn at an angle so you see the front
│   │   ├── Gravity      NO
│   │   ├── Examples     Classic Zelda, Pokémon, Stardew Valley
│   │   ├── Strengths    Top-down simplicity with more personality and depth
│   │   ├── Challenges   Needs Y-sorting (objects lower on screen draw in front)
│   !   └── Godot start  Same as top-down + enable Y Sort on the parent node
│
└── 📂 Isometric
    ├── Camera       True ~30° projection on a diamond-shaped grid
    ├── Gravity      NO
    ├── Examples     Diablo, Pharaoh, Transistor
    ├── Strengths    Strong depth without 3D, great for strategy/RPG, rich worlds
    ├── Challenges   Complex art, harder movement and pathfinding, camera consistency
    └── Godot start  TileMap with isometric tile shape (diamond)
```

**Beginner pick order, easiest to hardest:**

```
Side View  →  Top-Down  →  3/4 View  →  Isometric
```

---

### 📂 Genre_Hierarchy/

**The question to ask:** what does the player *want* to feel?

#### Primary Genres

```
📂 Primary_Genres/
│
├── Action ───── wants: SKILL & REFLEXES
│   └── Platformer, Beat 'em up, Shooter
│
├── Adventure ── wants: DISCOVERY
│   └── Point-and-click, Narrative-driven
│
├── RPG ──────── wants: GROWTH & CHOICES
│   └── JRPG, ARPG, Roguelike, Souls-like
│
├── Simulation ─ wants: MANAGEMENT
│   └── Life sim, City builder, Farming sim
│
├── Strategy ─── wants: PLANNING
│   └── RTS, Turn-based, Tower defense
│
├── Puzzle ───── wants: INSIGHT
│   └── Logic puzzle, Physics puzzle
│
└── Sports ───── wants: COMPETITION
    └── Racing, Football, Extreme sports
```

**RPG fix:** your note says RPG means "progresses through the story only". That's not quite right. An RPG is defined by **stats, progression, and player choices**. Story is common but optional (Diablo-likes have almost none).

---

#### Hybrids

```
📂 Hybrid_Subgenres/
│
├── Metroidvania        = Action + Platformer + Exploration
├── Survival RPG        = RPG + Simulation
├── Puzzle-Adventure    = Puzzle + Narrative
└── Action-Roguelike    = Action + Roguelike progression
```

**Metroidvania fix:** it is **not** open world. It's an **interconnected map locked behind abilities** (double jump, dash, key items). You revisit old areas with new powers.

**Metroidvania vs RPG:**
- Metroidvania progression comes from **abilities** (what you can *do*).
- RPG progression comes from **stats and choices** (how *strong* you are).
- They overlap, but neither contains the other.

**Rules for hybrids:**
1. At most **one** hybrid.
2. Both parents must work in the **same perspective**.
3. Not for games 1 to 3. Master one genre first.

### Perspective × Genre fit chart

```
                Side   TopDown   3/4    Iso
Action           ✅      ✅       ✅     ⚠️
Platformer       ✅      ❌       ❌     ❌
Metroidvania     ✅      ⚠️       ⚠️     ❌
RPG              ⚠️      ✅       ✅     ✅
Roguelike        ⚠️      ✅       ✅     ⚠️
Strategy         ❌      ✅       ✅     ✅
Simulation       ❌      ✅       ✅     ✅
Puzzle           ✅      ✅       ⚠️     ⚠️
Sports/Racing    ⚠️      ✅       ⚠️     ❌

✅ natural fit   ⚠️ possible but harder   ❌ rarely used
```
