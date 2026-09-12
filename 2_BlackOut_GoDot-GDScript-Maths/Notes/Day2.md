# Day 0 — Setup & Orientation (Simple Notes)
## The Big Analogy

Think of your game like building with LEGO:

- **Node** = a single LEGO brick (or a small pre-built part, like a wheel or a minifigure). Each type does one job.
- **Scene** = a finished LEGO *build* — bricks assembled into something, saved as its own instruction booklet (`.tscn` file). Every build needs one main brick it's all attached to — the **root node**.
- **Script (`.gd`)** = a little robot brain you clip onto ONE specific brick, telling that brick what to do ("when pressed, move forward").
- **Signal** = a doorbell on a brick. When something happens to that brick, it rings the bell. Other bricks can choose to "answer the doorbell" (connect a function) without the ringing brick knowing or caring who's listening.
- **Viewport** = the display case / window you look through to see the LEGO build. It has a fixed size — anything outside it, you just can't see.
- **Instancing a scene** = buying a second copy of a LEGO set box and placing it inside a bigger city you're building. Change the original box's design → every copy placed around your city updates too, After creating player.tscn we instantiate it into main.tscn.

---

## Definitions (plain language)

| Term             | Simple definition                                                               | Analogy                                     |
| ------------------| ---------------------------------------------------------------------------------| ---------------------------------------------|
| **Node**         | A single object with built-in behavior (draw an image, detect collisions, etc.) | One LEGO brick                              |
| **Scene**        | A saved tree of nodes, with one root node                                       | A finished LEGO build / instruction booklet |
| **Root node**    | The one node every other node in a scene is attached under                      | The base plate everything else sits on      |
| **Viewport**     | The rectangle the game is actually rendered into                                | A display case window                       |
| **`.tscn` file** | The saved scene — the tree + each node's settings                               | The instruction booklet itself              |
| **`.gd` file**   | Code attached to one node                                                       | The "brain" clipped onto one brick          |
| **Script**       | Defines what a *specific node* does                                             | The brain's instructions                    |
| **Signal**       | A notification a node sends out when something happens to it                    | A doorbell                                  |
| **Instancing**   | Placing a saved scene as a copy inside another scene                            | Placing a bought LEGO set inside your city  |

---

## My Two Doubts, Answered Simply

**Q: Can one scene have multiple scripts? What do scripts/signals do? Do scripts control a scene or a node?,Remember always there is only one root node,main.tscn which has multiple scenes**
- One *node* = max one script (one brick, one brain).
- One *scene* = many nodes = potentially many scripts, one per node.
- A script = behavior for that one node.
- A signal = a doorbell — notifies other nodes without direct wiring.
- Scripts control individual nodes, not "the scene" as a whole — the scene's behavior is just all its nodes' behaviors added together.
- One scene, one own script to control child nodes.

**Q: Why does making a Scene auto-create a Node, but not the reverse? What's "Save Branch as Scene" about?, better answer at ./Day3/1_Scene.md**
- A scene *must* have a root node to exist — like a LEGO build must have a base. So Godot always makes you pick a root node type the moment you create a scene.
- A loose node in the Node Dock doesn't need its own scene file — it can just live inside whatever scene is currently open, until you choose to save it separately.
- "Save Branch as Scene" = "cut this sub-assembly out of my current build and turn it into its own reusable LEGO box." It works on any child node.
- It does NOT work on the root node, because the root node already IS the whole box — you can't put a box inside itself.
- **Tried it hands-on:** added a throwaway child node ("TestChunk"), right-clicked it → Save Branch as Scene → worked fine, created `TestChunk.tscn`, and TestChunk became an *instance* of that new file (visible as a different icon in the tree). Deleted it afterward — just a test.
- A scene requires root node, but a root node doesnt require a scene.

**Q: Can we have multiple scenes, Yes!, you can have multiple scenes, but there is only one main scene that is the root node, and how do we make a 2d game, like scenes are stacked upon each other?**
  Two different meanings of "stacked" are worth separating here, because they answer different questions:
  
  So there are two completely separate things you might mean by "stacked," and they don't always match:
  
  **1. Multiple scenes, nested (structural)**
  Yes — you already know this one. `Main.tscn` will *contain* an instance of `Player.tscn`, which might contain an instance of `Hitbox.tscn`. Like Russian nesting dolls: a scene inside a scene inside a scene. That's "stacking" in the tree sense — parent/child ownership.
  
  **2. What's drawn in front of what (visual layering)**
  Separate concept, controlled by two things:
  - **Node order in the tree** — for regular 2D nodes, a node *lower* in the list of siblings draws *on top of* one that's higher up. So in Main's tree, if `Background` is listed before `Player`, Player draws in front of Background automatically. No extra setup needed — it's just list order.
  - **`CanvasLayer`** — the exception. Anything under a `CanvasLayer` node ignores normal draw order and always renders on top of everything else in that layer, regardless of where it sits in the tree. This is how UI/HUD elements stay visible no matter what the player or camera is doing — you'll use this for your health/water meter UI later.
  
  **Making a 2D game, in short:** you build a bunch of small scenes (Player, Enemy, a level), instance them into a bigger scene (a level or Main), position them with x/y coordinates in the same 2D space, and let node order + `CanvasLayer` decide what's visually in front. That's genuinely most of what "making a 2D game" *is*, structurally — everything else (movement, combat, co-op networking) is behavior layered on top of this skeleton.

---

## Instructions to Actually Finish Day 0

**Goal:** Godot installed, folders set up, one empty scene created + saved + set as main.

1. Install Godot 4.x (if not already).
2. Create the project with this folder structure:
   ```
   keep-ostriching/
   ├── project.godot
   ├── scenes/ (main/, player/, enemies/, ui/, net/, levels/)
   ├── scripts/
   ├── assets/ (art/, audio/, fonts/)
   └── resources/
   ```
3. Create a new empty scene, pick a root node (e.g. `Node2D`), rename it `Main`, save it as `scenes/main/Main.tscn`.
4. Go to **Project → Project Settings → Application → Run → Main Scene**, and set it to `Main.tscn`.

**Then set up the Viewport (the "display window" size):**

5. **Project → Project Settings → General → Display → Window.**
6. Set **Viewport Width** = 640, **Viewport Height** = 360 (small, simple 2D resolution).
7. Under **Stretch**, set **Mode** = `canvas_items`, **Aspect** = `keep` (so resizing the window doesn't distort things).
8. Close Settings, open `Main.tscn` — you'll see a dashed rectangle = your 640x360 viewport.
9. Add a `ColorRect` as a child, size it (640, 360), position (0,0), so it fills the rectangle.
10. Press **F6** to run the scene — the popup window should exactly match that dashed rectangle.

---

## Done-Check ✅
- [ ] Godot installed
- [ ] Folder structure created
- [ ] `Main.tscn` created, saved, and set as main scene
- [ ] Viewport set to 640x360, stretch mode = canvas_items / keep
- [ ] F6 popup window matches the dashed rectangle in the editor