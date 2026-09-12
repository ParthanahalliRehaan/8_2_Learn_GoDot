# Godot Basics — Do This Before Starting Any Project

A zero-assumed, click-by-click checklist. Go in order. Actually do each step in the editor before moving to the next.

---

## 1. Open Godot, create/open your project
Launch Godot. On the Project Manager screen, click your project to open it (or "Import" if it's not listed, pointing to your `project.godot` file). This opens the main editor window.

---

## 2. Learn the 4 panels you'll live in

| Panel | Location | What it shows |
|---|---|---|
| **Scene Dock** (a.k.a. Node Dock) | Top-left | The tree of nodes in the CURRENTLY OPEN scene |
| **FileSystem Dock** | Bottom-left | Every FILE in your project (all `.tscn`, `.gd` files on disk) |
| **Inspector** | Right side | The PROPERTIES of whatever node you have selected in the Scene Dock |
| **2D/3D Viewport editor** | Center | A visual canvas where you drag, resize, and position whatever node is selected |

Everything you do in Godot is some combination of: pick a file (FileSystem) → see its tree (Scene Dock) → click a node (Scene Dock) → edit it (Inspector or drag on the canvas).

---

## 3. Create your first scene
**Scene menu (top bar) → New Scene.** Pick a root node type — click the "2D Scene" shortcut button (gives you a `Node2D` root automatically). This root node now appears in the Scene Dock, empty, no children yet.

---

## 4. Add a ColorRect as a child node
In the Scene Dock, right-click your root node → **Add Child Node** → type `ColorRect` in the search → select it → Create.

**Why ColorRect?** It's the simplest possible visual node — just a solid colored rectangle, no image file needed. Beginners use it as a stand-in for real art (a red box = "this will be the player," a green box = "this will be a platform") so you can build and test layout/movement/collision before you have any actual sprites drawn. You'll replace these boxes with real images later (Day 12 in your plan).

---

## 5. Modify it using the Inspector
Click the ColorRect in the Scene Dock to select it. The right-side **Inspector** panel now shows fields like Color, Size, Position.
- Click the color swatch to change its color.
- Click into the Size fields and type numbers (e.g. 100, 100) to resize it.

You'll see it change live in the center 2D viewport. This is how you'll modify almost every node from now on: **select in Scene Dock → edit in Inspector** (or drag directly on the canvas with the mouse).

---

## 6. Save the scene & set it as Main Scene
`Ctrl+S` (or Scene → Save Scene). Name it `Main`, save into `scenes/main/`.

Then: **Project → Project Settings → Application → Run → Main Scene** → set it to this `Main.tscn`. This tells Godot "when I press Play, start here."

---

## 7. Set your Viewport size
**Project → Project Settings → General → Display → Window.**
- Set Viewport Width/Height (try 640 x 360).
- Set Stretch Mode = `canvas_items`, Aspect = `keep`.

This is your game's actual pixel canvas — the rectangle everything gets drawn inside, and what shows up in the game window when you hit Play. Stretch/Aspect settings stop resizing the window from distorting things.

---

## Done-Check ✅
- [ ] Project opens in the editor
- [ ] Can identify Scene Dock / FileSystem Dock / Inspector / 2D Viewport on sight
- [ ] `Main.tscn` created, saved, has a ColorRect child
- [ ] ColorRect's color/size successfully changed via Inspector
- [ ] `Main.tscn` set as Main Scene in Project Settings
- [ ] Viewport set to 640x360, stretch = canvas_items / keep
- [ ] Input Map has `move_left`, `move_right`, `jump` actions bound