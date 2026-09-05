# Dont Worry/
  - The below information isn't about 3D!
# Some Qns to be answered in GDD, reference below!
# Game Mode (Multiplayer Or Singleplayer)
# Game Type (2D or 3D)
# 📂 Game_Perspective/
```
📂 Game_Perspectives/
│
├── 📂 Isometric/
│   ├── View_Angle
│   │   └── Diagonal 3/4 perspective (pseudo‑3D look)
│   ├── Strengths
│   │   ├── Depth perception without full 3D
│   │   ├── Great for strategy/RPGs
│   │   └── Rich world‑building
│   ├── Challenges
│   │   ├── Asset creation more complex
│   │   ├── Movement/pathfinding harder
│   │   └── Camera consistency issues
│
├── 📂 TopDown/
│   ├── View_Angle
│   │   └── Direct overhead (bird’s‑eye)
│   ├── Strengths
│   │   ├── Simple navigation
│   │   ├── Clear visibility of environment
│   │   └── Works well for RPGs, roguelikes
│   ├── Challenges
│   │   ├── Less sense of depth
│   │   └── Can feel flat visually
│
└── 📂 Platformer/
    ├── View_Angle
    │   └── Side‑view (2D horizontal plane)
    ├── Strengths
    │   ├── Intuitive controls (left/right/jump)
    │   ├── Strong focus on character action
    │   └── Classic genre appeal
    ├── Challenges
    │   ├── Limited exploration (mostly linear)
    │   ├── Level design must stay engaging
    │   └── Less environmental immersion
```
# 📂 Genre_Hierarchy/
```
📂 Genre_Hierarchy/
│
├── 📂 Primary_Genres/
│   ├── Action
│   │   └── Subgenres: Platformer, Beat 'em up, Shooter
│   ├── Adventure
│   │   └── Subgenres: Point-and-click, Narrative-driven
│   ├── Role-Playing (RPG), Its a story driven game, where you let player have controls but progresses throught the story only!
│   │   └── Subgenres: JRPG, ARPG, Roguelike, Souls-like
│   ├── Simulation
│   │   └── Subgenres: Life sim, City builder, Farming sim
│   ├── Strategy
│   │   └── Subgenres: RTS, Turn-based, Tower defense
│   ├── Puzzle
│   │   └── Subgenres: Logic puzzle, Physics puzzle
│   └── Sports
│       └── Subgenres: Racing, Football, Extreme sports
│
└── 📂 Hybrid_Subgenres/
    ├── Metroidvania (Action-Adventure + Platformer + Exploration(Open world!))
    ├── Survival RPG (RPG + Simulation)
    ├── Puzzle-Adventure (Puzzle + Narrative)
    └── Action-Roguelike (Action + Roguelike progression)
```
## Metroidvania Vs RPG
- Metroidvania is more about exploration and ability-based progression, while RPGs are about stats, choices, and role-playing. They overlap, but one doesn’t fully encompass the other.
# 📂 Game Mechanics/
```
📂 Game_Mechanics/
│
├── 📂 TopDown/
│   ├── Movement
│   │   ├── 8-directional walking
│   │   ├── Sprint/dash
│   │   └── Pathfinding (AI navigation)
│   ├── Combat
│   │   ├── Melee attacks (sword, staff)
│   │   ├── Ranged attacks (bows, spells)
│   │   └── Area-of-effect skills
│   ├── Exploration
│   │   ├── Fog of war / map reveal
│   │   ├── Item collection
│   │   └── NPC interaction
│   ├── Progression
│   │   ├── Inventory system
│   │   ├── Leveling / stats
│   │   └── Quest tracking
│   └── Environment
│       ├── Destructible objects
│       ├── Puzzle elements (switches, doors)
│       └── Resource gathering
│
└── 📂 Platformer/
    ├── Movement
    │   ├── Run / walk
    │   ├── Jump / double jump
    │   ├── Wall climb / wall jump
    │   └── Dash / slide
    ├── Combat
    │   ├── Melee combos
    │   ├── Projectile attacks
    │   └── Enemy AI patterns
    ├── Exploration
    │   ├── Hidden paths / secrets
    │   ├── Ability-gated progression
    │   └── Collectibles (coins, power-ups)
    ├── Progression
    │   ├── Health upgrades
    │   ├── Skill unlocks
    │   └── Checkpoints / save system
    └── Environment
        ├── Moving platforms
        ├── Traps (spikes, fire)
        └── Physics puzzles
```
# Game Platform(Mobile/Console/PC/Web)
# Game Maths
## 🔢 Core Vector & Geometry Math
- **Dot product** — measuring similarity between directions (e.g., aiming, field of view checks).
- **Cross product (2D pseudo-cross)** — determining clockwise vs. counterclockwise orientation.
- **Projection** — projecting one vector onto another (useful for sliding along surfaces).
- **Angle between vectors** — rotation, aiming, steering behaviors.
- **Polar coordinates** — converting between angle/radius and Cartesian coordinates.

---

## ⏱ Motion & Physics
- **Acceleration & deceleration** — velocity integration with forces.
- **Friction & drag models** — exponential decay vs. linear resistance.
- **Elastic collisions** — reflection vectors, bounce angles.
- **Gravity wells** — inverse-square law attraction in 2D.
- **Spring-damper systems** — oscillations, smooth camera following.

---

## 🎲 Randomness & Probability
- **Weighted random selection** — loot tables, enemy spawns.
- **Noise functions (Perlin/Simplex)** — procedural terrain, smooth randomness.
- **Poisson distribution** — natural spacing for objects (trees, enemies).
- **Random walk / Brownian motion** — wandering AI.

---

## 🟦 Collision & Spatial Math
- **Circle-circle intersection** — radial overlap checks.
- **AABB (axis-aligned bounding box)** — fast rectangle overlap.
- **OBB (oriented bounding box)** — rotated rectangle collision.
- **Line intersection** — bullets, raycasts, visibility checks.
- **Spatial partitioning** — grids, quadtrees for efficient collision detection.

---

## ⏳ Timing & Simulation
- **Fixed timestep vs. variable timestep** — stability in physics.
- **Interpolation & extrapolation** — smooth rendering between updates.
- **Cooldowns & delays** — math as elapsed vs. duration comparisons.
- **Easing functions** — smooth transitions (ease-in, ease-out, bounce).

---

## 🎮 Gameplay-Oriented Math
- **Pathfinding heuristics** — Manhattan vs. Euclidean distance in A*.
- **Steering behaviors** — seek, flee, arrive, wander.
- **Camera math** — lerping, screen shake via sinusoidal offsets.
- **Parallax scrolling** — relative speed layers.
- **Screen-space transformations** — converting world to screen coordinates.

---

## 🧮 Advanced Topics
- **Matrix transformations** — rotation, scaling, translation in 2D.
- **Homogeneous coordinates** — handling translations with matrices.
- **Complex numbers** — elegant representation of 2D rotations.
- **Fourier transforms (basic)** — analyzing periodic signals (e.g., sound or wave motion).
- **Optimization math** — bounding volume hierarchies, broad-phase vs. narrow-phase collision.
