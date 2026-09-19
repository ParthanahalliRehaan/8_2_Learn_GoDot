**Doubt 1 — How does Godot load and compile scenes/nodes?**

Two separate things happen, and conflating them is where confusion usually starts:

- **`.tscn` files are text, not bytecode.** Open one in a text editor sometime — you'll see human-readable resource blocks describing node types, their properties, and which script (if any) is attached. Godot's `ResourceLoader` parses this text into memory as a **description**, not live objects.
- **`.gd` scripts *are* compiled** — into an internal bytecode Godot's VM executes (not native machine code, but not re-parsed line-by-line every frame either). This compile step happens when the script resource is loaded/parsed, cached by the engine.
- **Import cache (`.godot/` folder):** textures, sounds, etc. get imported into engine-native formats once, cached, and reused — this is why your first run after adding a new asset is slower than subsequent runs.

So the sequence for a scene is: parse `.tscn` text → build a blueprint (see Doubt 2) → when you actually need it in the tree, walk that blueprint and create real `Node` objects with their scripts attached and properties set.
