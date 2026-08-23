## 📘 GODOT SIGNALS & RESOURCES — REFERENCE CARD

---

### 1. WHAT IS A SIGNAL?

A **signal** is a **doorbell for your code**.

| Part | Analogy | In Code |
|------|---------|---------|
| The bell button | Something happens | `emit()` |
| The wiring | Linking button to sound | `connect()` |
| The bell sound | Reacting to the event | Your callback function |

**The key insight:** The person pressing the doorbell doesn't know who's answering. They just ring it. This is called **decoupling** — your player doesn't need to know about the HUD, sound manager, or game over screen.

---

### 2. THE THREE PARTS OF A SIGNAL

#### **A. DECLARE** — Install the doorbell
```gdscript
# In EntityStats.gd
signal health_changed(new_health: int, max_health: int)
signal entity_died()
```
- `signal` = keyword
- `health_changed` = event name
- `(new_health: int, max_health: int)` = data sent with the event

#### **B. EMIT** — Ring the bell
```gdscript
var current_health: int:
    set(value):
        current_health = clampi(value, 0, max_health)
        health_changed.emit(current_health, max_health)   # ← RING!
        if current_health <= 0:
            entity_died.emit()                             # ← RING!
```
- `.emit()` = fires the signal
- Arguments = data passed to all listeners

#### **C. CONNECT** — Wire the bell to a listener
```gdscript
# In your HUD, test scene, or sound manager:
func _ready():
    stats.health_changed.connect(_on_health_changed)
    stats.entity_died.connect(_on_entity_died)

func _on_health_changed(new_health: int, max_health: int):
    print("Health: %d/%d" % [new_health, max_health])

func _on_entity_died():
    print("💀 Entity died!")
```

---

### 3. CUSTOM SETTER (`set`) — THE MAGIC BEHIND HEALTH

| Normal Variable | Custom Setter |
|-----------------|---------------|
| `health = 50` — just stores 50 | `health = 50` — runs code too |

```gdscript
var current_health: int:
    set(value):                    # ← This block runs on EVERY assignment
        current_health = clampi(value, 0, max_health)  # Clamp 0 to max
        health_changed.emit(current_health, max_health) # Notify listeners
        if current_health <= 0:
            entity_died.emit()      # Death announcement
```

**What happens when you call `take_damage(30)` at full health:**
1. `current_health -= 30` → setter receives `value = 70`
2. `clampi(70, 0, 100)` → stores `70`
3. `health_changed.emit(70, 100)` → "Health is now 70/100!"
4. `70 <= 0?` No → death signal doesn't fire

**What happens when you call `take_damage(50)` at health 20:**
1. `current_health -= 50` → setter receives `value = -30`
2. `clampi(-30, 0, 100)` → stores `0` (can't go below 0)
3. `health_changed.emit(0, 100)` → "Health is now 0/100!"
4. `0 <= 0?` YES → `entity_died.emit()` → "Entity died!"

---

### 4. WHAT IS A `Resource`?

A **blueprint for data** — not a physical object, just numbers and settings.

| `extends Node` | `extends Resource` |
|----------------|-------------------|
| Needs scene tree | No scene tree needed |
| Has `_process()`, `_physics_process()` | Only data + logic |
| Heavy (transform, collision, etc.) | Lightweight |
| Can't save standalone | Save as `.tres` file |
| Not Inspector-editable | Fully editable in Inspector |

**Analogy:** A `Node` is a car (has an engine, moves, makes noise). A `Resource` is the car's spec sheet (horsepower, weight, color) — you can photocopy it and hand it to anyone.

---

### 5. WHAT IS `test_entity.tres`?

It's a **saved file on disk** containing one instance of `EntityStats`.

**How to create it:** Right-click in FileSystem → New Resource → Search `EntityStats` → Create → Name it `test_entity.tres`

**What's inside (human-readable):**
```ini
[gd_resource type="Resource" script_class="EntityStats"]
script = ExtResource("1_abc123")
max_health = 150
current_health = 150
```

**Why use it:**
- Create 50 enemies, each with a different `.tres` (unique stats)
- Designers tweak values in Inspector without touching code
- Share the same stats across multiple scenes

---

### 6. WHAT IS `class_name`?

Makes your script a **global type** — usable anywhere without `preload()`.

| Without `class_name` | With `class_name EntityStats` |
|----------------------|-------------------------------|
| `preload("res://...").new()` | `EntityStats.new()` anywhere |
| No autocomplete | Autocomplete works everywhere |
| Path can break if moved | Path doesn't matter |
| Not in "New Resource" list | Shows in "New Resource" dialog |

---

### 7. WHAT IS THE TEST SCENE?

```
test_entity_stats.tscn
    └── Node2D (root)
        └── Script: TestEntityStats.gd
```

**Purpose:** A **sandbox** to verify `EntityStats` works before using it in your real game.

**Why bother:**
- **Isolate bugs** — If something breaks, you know it's `EntityStats`, not player movement
- **Fast iteration** — No need to run the full game
- **Living documentation** — Future you opens this and remembers how it works
- **Safe experiments** — Try `heal(999)` or `take_damage(-50)` without breaking anything

---

### 8. VISUAL ARCHITECTURE

```
    Player ──uses──┐
    Enemy  ──uses──┼──▶ EntityStats (the "brain")
    Boss   ──uses──┘         │
                    health_changed.emit() ──▶ HUD (health bar)
                    entity_died.emit() ─────▶ GameOver Screen
                                          ──▶ SoundManager (death SFX)
```

**The rule:** `EntityStats` **announces**. Others **listen**. No direct references. Fully decoupled.

---

### 9. CHEAT SHEET

| Concept | One-Line Summary |
|---------|-----------------|
| `signal` | Declare an event that can be emitted |
| `.emit()` | Fire the signal, notify listeners |
| `.connect()` | Wire a function to hear the signal |
| `set(value)` | Custom code that runs on every assignment |
| `extends Resource` | Lightweight data container |
| `class_name` | Make type globally available |
| `.tres` file | Saved resource instance on disk |
| Test scene | Sandbox to verify code works |

---

### 10. COMMON MISTAKES

| ❌ Mistake | ✅ Fix |
|-----------|--------|
| Emitting before connecting | Connect in `_ready()`, emit after |
| Connecting twice | Use `CONNECT_ONE_SHOT` or check `is_connected()` |
| Using `extends Node` for data | Use `extends Resource` for stat containers |
| Forgetting `clampi` in setter | Always clamp health so it doesn't go negative |
