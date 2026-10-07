## 🧾 Enums in GDScript — Final Notes

### 1. What Enums Are
- **Enums = named integer constants**.
- They replace “magic numbers” with meaningful names.
- Default values start at `0` and increment automatically unless explicitly set.

```gdscript
enum Direction { UP, RIGHT, DOWN, LEFT }
```

---

### 2. Exporting Enums to the Inspector
- Syntax: `@export (EnumName) var variable`
- `(Week)` in your example means:  
  *“This exported variable can only take values from the `Week` enum.”*
- In the Inspector, this shows up as a **dropdown menu** with enum options.

```gdscript
enum Week { SUNDAY, MONDAY, TUESDAY }
@export (Week) var chosen_day = Week.SUNDAY
```

---

### 3. Usage Patterns
- **Basic comparison**
  ```gdscript
  if chosen_day == Week.MONDAY:
      print("Back to work")
  ```
- **Explicit values**
  ```gdscript
  enum DamageType { PHYSICAL = 10, FIRE = 20 }
  ```
- **Match statement**
  ```gdscript
  match chosen_day:
      Week.SUNDAY: print("Weekend!")
      Week.MONDAY: print("Workday")
  ```

---

### 4. Best Practices
- **Naming convention**: Enum name in CamelCase, items in UPPERCASE.
- **Explicit values**: Assign numbers manually if enums are saved/serialized (to avoid breaking compatibility).
- **Avoid repurposing**: Don’t change the meaning of existing values once in use.

---

### 5. Common Doubts Cleared
- **Why `(Week)`?** → It restricts the exported variable to that enum, giving you a dropdown in the editor.
- **Are enums just numbers?** → Yes, but with names attached for clarity.
- **Can I use multiple enums?** → Absolutely, just declare them separately:
  ```gdscript
  enum Week { SUNDAY, MONDAY }
  enum GameState { IDLE, RUNNING, JUMPING }
  ```
- **Can enums be iterated?** → Yes, they’re dictionaries:
  ```gdscript
  for key in Week.keys():
      print(key, Week[key])
  ```

---

✅ **In summary:**  
Enums in GDScript are named integer constants. `(Week)` in `@export (Week)` is a type hint that makes the variable selectable via a dropdown in the Inspector. Use enums for states, categories, and modes to keep your code clean, safe, and editor-friendly.
