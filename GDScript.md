# GDScript Comprehensive Guide (Godot 4.x)

A practical, example-driven reference for learning GDScript — from variables to object-oriented design.

---

## 1. Variables and Data Types

### Declaring Variables
GDScript variables are declared with `var`. Declaration and assignment can happen together or separately.

```gdscript
var health          # declared, defaults to null
health = 100         # assigned later
var speed = 5.0       # declared and assigned together
```

### Typing Styles
GDScript supports three typing approaches:

```gdscript
var health: int = 100    # explicit typing — clearest, best for tooling/errors
var health := 100         # inferred typing — type is locked based on the assigned value
var health = 100           # untyped/dynamic — most flexible, least safe
```

- **Explicit typing** (`: int`) is best when the type isn't obvious from context or you want a specific type (e.g., `float` instead of inferred `int`).
- **Inferred typing** (`:=`) is a shortcut that still gives you static-typing benefits (autocomplete, compile-time errors) with less typing.
- **Untyped** variables can hold anything and change type at runtime — useful for generic containers but riskier.

**Why it matters:** Typed variables let the editor catch type mismatches before you run the game, and they make autocomplete far more useful.

### Constants
Use `const` for values that never change.

```gdscript
const MAX_HEALTH: int = 100
const GRAVITY := 980.0
```

### Core Built-in Types

```gdscript
var lives: int = 3
var speed: float = 4.5
var is_alive: bool = true
var player_name: String = "Aria"
var inventory: Array = ["sword", "shield", "potion"]
var stats: Dictionary = {"strength": 10, "agility": 8}
var position: Vector2 = Vector2(100, 200)      # 2D position
var world_pos: Vector3 = Vector3(1, 2, 3)       # 3D position
var tint: Color = Color(1, 0, 0)                 # red sprite tint
```

Realistic use cases:
- `Vector2` — moving a 2D sprite: `position += Vector2(speed, 0) * delta`
- `Color` — flashing a sprite red on damage: `sprite.modulate = Color.RED`

### Type Conversion Gotchas

```gdscript
var a: int = 7
var b: int = 2
print(a / b)          # 3 — integer division truncates!
print(float(a) / b)    # 3.5 — cast one side to float for real division
```

**Beginner mistakes to watch for:**
- Dividing two `int`s and being surprised by truncation instead of a decimal result.
- Mixing untyped and typed variables and getting runtime errors instead of compile-time warnings.

---

## 2. Arrays and Dictionaries

### Arrays
Arrays are ordered, mutable, zero-indexed collections.

```gdscript
var enemies: Array = ["Goblin", "Skeleton", "Orc"]

enemies.append("Dragon")       # add to end
enemies.insert(1, "Slime")      # insert at index 1
enemies.erase("Skeleton")        # remove by value
enemies.pop_back()                # remove and return last element

print(enemies[0])                 # access by index
print(enemies.size())             # number of elements
```

### Typed Arrays (Godot 4)
Typed arrays restrict what can be stored, catching bugs early and improving performance.

```gdscript
var scores: Array[int] = [10, 20, 30]
var names: Array[String] = ["A", "B"]
```

### Dictionaries
Dictionaries store key-value pairs.

```gdscript
var player_stats: Dictionary = {
    "hp": 100,
    "mp": 50,
    "level": 1
}

print(player_stats["hp"])          # direct access — errors if key missing
print(player_stats.get("mp", 0))    # safe access with a default fallback

for key in player_stats:
    print(key, ":", player_stats[key])
```

### Combining Both: Inventory Example

```gdscript
var inventory: Array[Dictionary] = [
    {"name": "Sword", "damage": 15},
    {"name": "Shield", "defense": 10}
]

for item in inventory:
    print(item["name"])
```

**Beginner mistakes to watch for:**
- Using `dict["key"]` on a possibly-missing key and crashing instead of using `.get()`.
- Forgetting that arrays are passed by reference, so modifying a copy of an array variable can unexpectedly modify the original.

---

## 3. Control Flow

### if / elif / else

```gdscript
var player_hp: int = 40

if player_hp <= 0:
    print("Player is dead")
elif player_hp < 50:
    print("Player is low on health")
else:
    print("Player is healthy")
```

### match (switch-like structure)

```gdscript
var state: String = "jumping"

match state:
    "idle":
        print("Standing still")
    "running":
        print("Moving fast")
    "jumping":
        print("In the air")
    _:
        print("Unknown state")
```

`match` also supports pattern matching on arrays and dictionaries:

```gdscript
var input := [1, 0]

match input:
    [1, 0]:
        print("Move right")
    [0, 1]:
        print("Move up")
    _:
        print("No movement")
```

### Loops

```gdscript
for i in range(5):        # 0 through 4
    print(i)

for enemy in enemies:      # iterate directly over array elements
    print(enemy)

var count := 0
while count < 3:
    print(count)
    count += 1
```

### break and continue

```gdscript
for i in range(10):
    if i == 3:
        continue    # skip this iteration
    if i == 7:
        break        # exit the loop entirely
    print(i)
```

**Beginner mistakes to watch for:**
- Using `match` but forgetting the wildcard `_` case, so unexpected values silently do nothing.
- Confusing `continue` (skip this iteration) with `break` (exit the loop).

---

## 4. Functions and Typing

### Basic Function Syntax

```gdscript
func greet(name: String) -> void:
    print("Hello, " + name)
```

### Default Parameters and Return Types

```gdscript
func take_damage(amount: int, is_critical: bool = false) -> int:
    var total := amount
    if is_critical:
        total *= 2
    return total
```

### Scope: Local vs Global (Script-Level)

```gdscript
var player_score: int = 0    # script-level ("global" to this script)

func add_score(points: int) -> void:
    var bonus := 10           # local — only exists inside this function
    player_score += points + bonus
```

`self` refers to the current instance and is often optional, but useful for clarity or when a local variable shadows a member variable.

### Static Functions

```gdscript
static func double(value: int) -> int:
    return value * 2
```

Static functions don't need an instance of the class and can't access instance variables — useful for utility/helper functions.

### Untyped vs Typed Example

```gdscript
# Untyped — bug not caught until runtime
func add(a, b):
    return a + b

add("5", 3)     # runtime error or unexpected behavior

# Typed — bug caught immediately by the editor
func add(a: int, b: int) -> int:
    return a + b

add("5", 3)     # editor error before you even run the game
```

**Beginner mistakes to watch for:**
- Forgetting `-> void` on functions that don't return anything, making intent unclear.
- Shadowing a script-level variable with a local variable of the same name and wondering why changes don't persist.

---

## 5. Object-Oriented Programming (OOP)

### Every Script Is a Class

Every `.gd` file is implicitly its own class. `extends` sets its parent class (often a Godot node type).

```gdscript
extends CharacterBody2D

class_name Player     # registers this class globally, usable as a type elsewhere
```

Once `class_name` is declared, other scripts can reference `Player` as a type directly, without loading the file manually.

### Inheritance and Overriding

```gdscript
# enemy.gd
extends CharacterBody2D
class_name Enemy

func _ready() -> void:
    print("Enemy spawned")

func take_damage(amount: int) -> void:
    print("Enemy took damage:", amount)
```

```gdscript
# boss.gd
extends Enemy
class_name Boss

func _ready() -> void:
    super._ready()             # call the parent's _ready() first
    print("Boss music starts")

func take_damage(amount: int) -> void:
    super.take_damage(amount)    # reuse parent logic
    print("Boss is enraged!")
```

Common lifecycle methods you'll override constantly: `_ready()` (runs once when node enters the tree), `_process(delta)` (runs every frame), `_physics_process(delta)` (runs every physics tick).

### Signals

Signals let nodes communicate without tightly coupling them together.

```gdscript
# health.gd
extends Node
class_name Health

signal health_depleted
signal health_changed(new_value: int)

var current: int = 100

func take_damage(amount: int) -> void:
    current -= amount
    health_changed.emit(current)     # Godot 4 syntax
    if current <= 0:
        health_depleted.emit()
```

Connecting in code:

```gdscript
# player.gd
@onready var health: Health = $Health

func _ready() -> void:
    health.health_depleted.connect(_on_health_depleted)

func _on_health_depleted() -> void:
    print("Player has died")
```

You can also connect signals visually in the editor's Node panel. Signals are preferred over calling methods directly on other nodes because the emitting node doesn't need to know who's listening — this keeps components decoupled and reusable.

### Encapsulation Conventions

```gdscript
extends Node

var _internal_counter: int = 0    # underscore = "private" by convention (not enforced)

@export var max_speed: float = 200.0    # exposed and editable in the Inspector
@onready var sprite: Sprite2D = $Sprite2D  # cached reference, set when node is ready

func get_counter() -> int:
    return _internal_counter

func _increment() -> void:
    _internal_counter += 1
```

- Underscore-prefixed names (`_internal_counter`) signal "internal use only" — GDScript doesn't enforce true privacy, it's a convention.
- `@export` exposes a variable in the editor Inspector, useful for designer-tunable values.
- `@onready` defers variable assignment until the node is ready, essential for grabbing references to child nodes safely.

### Best Practices for Organizing Code

- **One responsibility per script** — a `Health` script manages HP, not movement or input.
- **Favor composition over deep inheritance** — build a `Player` out of child nodes (`Health`, `Hitbox`, `StateMachine`) rather than one giant class inheriting through many layers.
- **Typical folder structure:**
  ```
  res://
  ├── scenes/
  │   ├── player/
  │   │   ├── player.tscn
  │   │   └── player.gd
  │   └── enemies/
  ├── scripts/
  │   ├── components/
  │   └── autoloads/
  └── assets/
  ```
- Use **autoloads** (singletons) sparingly, for truly global systems like a `GameManager` or `EventBus`.

**Beginner mistakes to watch for:**
- Forgetting to call `super()` when overriding `_ready()` or `_process()`, silently breaking parent behavior.
- Using deep inheritance chains (`Enemy -> FlyingEnemy -> FireFlyingEnemy -> ...`) instead of composing behavior from smaller reusable nodes/components.
- Connecting signals in code but forgetting to disconnect them when a node is freed, causing errors when the signal fires on a freed object.

---

## Quick Reference Cheat Sheet

| Concept | Syntax |
|---|---|
| Typed variable | `var hp: int = 100` |
| Inferred variable | `var hp := 100` |
| Constant | `const MAX_HP := 100` |
| Typed array | `var nums: Array[int] = [1, 2, 3]` |
| Dictionary access (safe) | `dict.get("key", default)` |
| Match statement | `match value:` |
| Typed function | `func heal(amount: int) -> void:` |
| Class name | `class_name Player` |
| Inheritance | `extends Enemy` |
| Call parent method | `super.method_name()` |
| Declare signal | `signal died` |
| Emit signal | `died.emit()` |
| Connect signal | `node.died.connect(_on_died)` |
| Export to Inspector | `@export var speed: float` |
| Cached node reference | `@onready var sprite = $Sprite2D` |