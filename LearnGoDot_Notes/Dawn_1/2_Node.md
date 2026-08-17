# Dawn 1, Part 2: Nodes, Properties, Files & Assets

The big picture first:

```text
                    GODOT PROJECT
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
       Scenes          Scripts         Assets
       (.tscn)          (.gd)       (.png/.svg/.glb...)
          │
          ↓
        Nodes
          │
    ┌─────┼─────────────┐
    ↓     ↓             ↓
 Sprite Camera      Collision
```

Godot describes nodes as the basic building blocks of a game, while scenes are trees of those nodes. ([Godot Engine documentation][1])

---

# 1. What exactly is `Sprite2D`?

A **Sprite2D is a node whose job is to display a 2D texture/image.**

Think:

```text
Sprite2D
    │
    └── Texture → player.png
```

The `Sprite2D` itself isn't your artwork.

It is the **thing that tells Godot:**

> "Take this image and render it here."

For example:

```text
player.png
     ↓
  Sprite2D
     ↓
appears in the game
```

Your image could be:

* a player
* a tree
* a sword
* a coin
* a rock
* an enemy
* a UI icon

All of those can be displayed using a Sprite2D.

### Important distinction

```text
PNG
 ↓
IMAGE / TEXTURE

Sprite2D
 ↓
NODE THAT DISPLAYS THAT TEXTURE
```

Godot's documentation describes textures as resources that nodes use, rather than nodes themselves. ([Godot Engine documentation][2])

---

# 2. What is `Camera2D`?

This one is conceptually very important.

A `Camera2D` represents the **viewpoint of the player in a 2D world**.

Imagine the game world is enormous:

```text
WORLD

┌───────────────────────────────────────┐
│                                       │
│       forest                          │
│                    PLAYER             │
│                       ↓               │
│                  ┌─────────┐          │
│                  │ CAMERA  │          │
│                  └─────────┘          │
│                                       │
│                         village       │
│                                       │
└───────────────────────────────────────┘
```

The camera determines roughly:

> **"Which part of this world should the player see?"**

Without a camera concept, your world and your screen aren't meaningfully connected.

For a player scene, you might eventually have:

```text
Player
├── Sprite2D
├── CollisionShape2D
└── Camera2D
```

Then the camera can follow the player.

---

# 3. `CollisionShape2D`

This is **not the visible artwork**.

It describes the shape used for collision/detection.

Imagine:

```text
       PLAYER IMAGE

        █████
      █████████
      █████████
        █████

       COLLISION

        ┌───┐
        │   │
        │   │
        └───┘
```

The image can look complicated.

The collision shape might simply be:

```text
Rectangle
Capsule
Circle
```

Godot uses `CollisionShape2D` to provide a `Shape2D` to a collision object. ([Godot Engine documentation][3])

So:

```text
Sprite2D
= what the player LOOKS like

CollisionShape2D
= what the physics system THINKS the player looks like
```

That's a very useful distinction.

---

# 4. `CharacterBody2D`

Now we're getting into an important architectural concept.

`CharacterBody2D` represents a **2D physics body intended to be controlled by code**. ([Godot Engine documentation][4])

So you might build:

```text
Player
(CharacterBody2D)
│
├── Sprite2D
├── CollisionShape2D
└── Camera2D
```

Think of the responsibilities:

| Node               | Responsibility                  |
| ------------------ | ------------------------------- |
| `CharacterBody2D`  | Player's physical/movement body |
| `Sprite2D`         | Player's appearance             |
| `CollisionShape2D` | Player's collision boundary     |
| `Camera2D`         | Player's viewpoint              |

That's much better than thinking of the player as one giant object.

You're **composing behavior from components**.

---

# 5. `Node2D`

`Node2D` is a fundamental 2D node.

It provides things like:

* position
* rotation
* scale
* skew
* drawing/render-order-related properties

Godot's `Sprite2D`, `Camera2D`, `CollisionShape2D`, etc. inherit from the 2D node hierarchy. ([Godot Engine documentation][5])

So you can mentally visualize:

```text
Node
 │
 └── Node2D
      │
      ├── Sprite2D
      ├── Camera2D
      ├── CollisionShape2D
      └── CharacterBody2D
```

Don't memorize the entire inheritance tree yet.

Just understand:

> **Node2D gives a node a 2D spatial presence.**

---

# 6. And the 3D equivalents

Godot has corresponding 3D concepts.

For example:

```text
Node3D
│
├── MeshInstance3D
├── Camera3D
├── CollisionShape3D
└── CharacterBody3D
```

### `MeshInstance3D`

This is roughly the 3D equivalent of the visual role of `Sprite2D`.

It displays a **3D mesh**.

For example:

```text
Mesh
 ↓
MeshInstance3D
 ↓
3D object appears
```

Your `.glb` files will become relevant here.

---

# 7. `Camera3D`

Same fundamental idea as `Camera2D`, but now the world has:

```text
X
Y
Z
```

instead of just:

```text
X
Y
```

The camera determines what portion of the 3D world gets rendered from its viewpoint.

This is where concepts like:

* perspective
* field of view
* projection
* depth
* camera rotation

become important.

And yes, this connects directly to the **projection angles** you were asking about earlier. That stuff isn't random math sprinkled over the game. It's describing how the 3D world gets projected onto the 2D screen.

---

# 8. Inspector properties

Now your earlier question about the **Inspector** becomes much more meaningful.

Select a `Sprite2D`, for example.

You might see properties such as:

```text
Sprite2D
────────────────

Texture
Position
Rotation
Scale
Offset
Flip
Visibility
Modulate
Z Index
```

These properties describe **how that node behaves or appears**.

### The most important one initially: Transform

For a 2D spatial node:

```text
Transform
├── Position
├── Rotation
└── Scale
```

### Position

Where is it?

```text
X = 200
Y = 150
```

### Rotation

Which direction is it facing?

```text
Rotation = 45°
```

### Scale

How large is it?

```text
X = 2
Y = 2
```

So:

```text
Position → WHERE
Rotation → WHICH DIRECTION
Scale    → HOW LARGE
```

These three concepts are fundamental to game development.

---

# 9. Now the file extensions

This is where beginners often get confused because they see:

```text
player.gd
player.tscn
player.glb
player.svg
player.png
```

and assume they're all "Godot files."

They aren't.

Some are **Godot-native files**.

Some are **assets created by external tools**.

---

# `.tscn`

### Meaning

**Text Scene**

A `.tscn` stores a Godot scene.

For example:

```text
Player.tscn
```

could represent:

```text
Player
├── Sprite2D
├── CollisionShape2D
└── Camera2D
```

Godot's documentation defines TSCN as the text-based format representing a scene tree. ([Godot Engine documentation][6])

### How do you make one?

You create nodes in Godot:

```text
Create nodes
     ↓
Arrange them
     ↓
Configure properties
     ↓
Save Scene
     ↓
Player.tscn
```

**You normally don't manually write `.tscn` files.**

Godot generates them.

---

# `.gd`

### Meaning

**GDScript**

This is Godot's scripting language.

Example conceptually:

```text
Player.tscn
     │
     └── Player.gd
```

The scene describes:

> **What the player is made of.**

The script describes:

> **What the player does.**

Godot's Script Editor can create a new `.gd` script for you. ([Godot Engine documentation][7])

So:

```text
.tscn → structure/configuration
.gd   → behavior/logic
```

You will write `.gd` files yourself.

And yes, **you will be writing these**, not me handing you finished code. That's the whole point of learning.

---

# `.png`

This is a normal image file.

For pixel art, this is probably going to be one of your most important formats.

```text
player.png
tree.png
sword.png
tileset.png
```

Godot imports PNGs as textures for rendering. ([Godot Engine documentation][8])

You generally create them in an image/pixel-art editor.

---

# `.svg`

**Scalable Vector Graphics**

Unlike pixel art, SVG is based on vector shapes rather than a fixed grid of pixels.

Think:

```text
PNG

pixels:
■■■■■■
■■■■■■
■■■■■■


SVG

mathematical shapes:
circle
line
curve
polygon
```

This means SVGs can scale without the same pixel-resolution limitations.

Godot can import SVG files as scalable textures. ([Godot Engine documentation][9])

Your current project already contains:

```text
icon.svg
```

That's the Godot project icon.

---

# `.glb`

Now we're entering **3D asset territory**.

`.glb` is the binary form of **glTF 2.0**.

Think:

```text
Blender
   ↓
3D model
   ↓
export
   ↓
model.glb
   ↓
Godot
   ↓
3D scene / assets
```

Godot recommends glTF 2.0 and supports both `.gltf` and `.glb`. ([Godot Engine documentation][10])

A `.glb` can contain things associated with a 3D asset, such as:

* meshes
* materials
* textures
* animations
* scene information

So if we create a 3D character in Blender, `.glb` is one of the formats we'd commonly bring into Godot.

---

# `.tres`

You'll eventually encounter this too.

`.tres` is a **Godot text resource file**.

Remember:

> Scene = a tree of nodes.

> Resource = data used by nodes.

Godot's documentation explicitly distinguishes Nodes from Resources. Resources hold data such as textures, meshes, animations, audio, and other reusable information. ([Godot Engine documentation][2])

So:

```text
.tscn
= Scene

.tres
= Resource
```

Don't worry about creating these manually yet.

---

# `.import` / `.godot`

You'll also see Godot-generated project data.

Don't casually edit or delete random generated files because you saw them and your human curiosity demanded sacrifice.

Godot uses imported resource data and project metadata internally.

For now, your important distinction is:

```text
YOU CREATE / EDIT

.gd
.tscn
.png
.svg
.glb
.wav
.ogg
```

versus:

```text
GODOT GENERATES / MANAGES

.godot/
import-related data
```

---

# 10. How do we actually make all these things?

Here's the pipeline I want you to understand.

## 2D game asset pipeline

```text
          YOU DRAW
             ↓
       Pixelorama
             ↓
        player.png
             ↓
         Godot imports
             ↓
          Sprite2D
             ↓
       Player.tscn
             ↓
        Player.gd
             ↓
           GAME
```

---

## 3D asset pipeline

```text
          YOU MODEL
             ↓
           Blender
             ↓
          model.glb
             ↓
         Godot imports
             ↓
       MeshInstance3D
             ↓
          Scene
             ↓
           GAME
```

---

## Scene pipeline

```text
Godot
 ↓
Create nodes
 ↓
Arrange hierarchy
 ↓
Configure Inspector
 ↓
Save
 ↓
.tscn
```

---

## Code pipeline

```text
Godot Script Editor
        ↓
     write code
        ↓
      .gd
        ↓
attached to Node
        ↓
changes node behavior
```

That's the overall asset architecture.

---

# 11. So how do we make actual game assets?

There are several categories.

### Pixel art

You draw:

* characters
* enemies
* weapons
* trees
* buildings
* UI icons
* tiles
* particles

Typical output:

```text
.png
```

### 3D art

You model:

* characters
* buildings
* weapons
* environments
* props

Typical output:

```text
.glb
```

### Audio

You create or obtain:

* footsteps
* attacks
* ambience
* music
* UI sounds

Typical formats:

```text
.wav
.ogg
```

### Fonts

You create or obtain fonts:

```text
.ttf
.otf
```

### Vector graphics

You create:

* logos
* icons
* scalable UI graphics

Typical format:

```text
.svg
```

---

# 12. Free pixel editor: Pixelorama

For **your situation**, I'd recommend **Pixelorama**.

It's free and open source, and it is specifically designed for pixel art, sprites, animations, tiles, and game graphics. It has versions for Windows, Linux, macOS, and even a browser version. ([Pixelorama][11])

[Pixelorama official website](https://pixelorama.org/?utm_source=chatgpt.com)

It supports things particularly useful for game development:

* frame-by-frame animation
* onion skinning
* spritesheets
* tiles
* pixel-perfect drawing
* layers
* palettes
* game-oriented export workflows ([Pixelorama][12])

So our pipeline can be:

```text
Pixelorama
     ↓
draw pixel art
     ↓
PNG
     ↓
Godot
     ↓
Sprite2D
```

That is a perfectly legitimate workflow.

---

# 13. But how do you actually CREATE pixel-art assets?

This is a separate skill from Godot.

Suppose we want a player.

Don't immediately start drawing a 512×512 masterpiece because apparently suffering is a required prerequisite for game development.

Start with something tiny.

For example:

```text
Canvas: 32 × 32 pixels
```

Then build in layers:

### Stage 1: Silhouette

```text
   ██
 ██████
 ██████
  ████
  ████
 ██  ██
```

Don't worry about detail.

You're establishing:

> **What is the shape?**

### Stage 2: Major color regions

```text
hair
skin
shirt
pants
boots
```

### Stage 3: Lighting

Determine:

```text
light → →
```

Then establish:

```text
light side
shadow side
```

### Stage 4: Details

Add:

* eyes
* belt
* weapon
* clothing details
* highlights

### Stage 5: Animation

Create frames:

```text
Frame 1 → standing
Frame 2 → left foot
Frame 3 → standing
Frame 4 → right foot
```

Then:

```text
Frame 1
   ↓
Frame 2
   ↓
Frame 3
   ↓
Frame 4
```

Godot can then use those frames for animation.

---

# 14. Assets aren't just "pictures"

This is an important game-development concept.

Consider a sword.

You need several things:

```text
Sword
│
├── Visual
│     └── sword.png
│
├── Collision
│     └── CollisionShape2D
│
├── Behavior
│     └── Sword.gd
│
└── Scene
      └── Sword.tscn
```

That's why game development is more than drawing pretty things.

You're connecting:

```text
ART
 +
STRUCTURE
 +
PHYSICS
 +
BEHAVIOR
 =
GAME OBJECT
```

And that is exactly why we're learning **nodes → scenes → resources → scripts** before jumping into gameplay.
