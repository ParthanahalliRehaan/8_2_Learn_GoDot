# Platformer Math Notes (Side View)

> **Scope:** this file covers **platformer only**. Top-down notes are not started yet, so we'll write them later when we reach that game. Anything in your math list that only matters for top-down (8-way vectors, steering, fog of war) is skipped here on purpose.

---

## 0. Ground rules

**Godot's coordinate system:** `+x` is right and `+y` is **down**. So:

```
Up    = Vector2(0, -1)     (Vector2.UP)
Down  = Vector2(0,  1)
Jump velocity is NEGATIVE, gravity is POSITIVE.
```

**The core loop of all platformer math:**

```
velocity += acceleration * delta     (forces change velocity)
position += velocity * delta         (velocity changes position)
```

`move_and_slide()` does the second line for you and handles collisions. You only write the first line.

**Units:** everything is in **pixels** and **seconds**. Speed = px/s, gravity = px/s².

**Where code goes:** movement lives in `_physics_process(delta)` (fixed timestep, stable). Always multiply by `delta`, except for one-time impulses like setting jump velocity.

---

## 1. Horizontal movement

### 1.1 Instant movement (rectangle prototype)
```gdscript
var dir = Input.get_axis("left", "right")     # -1, 0, or 1
velocity.x = dir * SPEED
```
Simple but feels stiff. Fine for day 1.

### 1.2 Acceleration and friction (the "game feel" version)
```gdscript
if dir != 0:
    velocity.x = move_toward(velocity.x, dir * SPEED, ACCEL * delta)
else:
    velocity.x = move_toward(velocity.x, 0, FRICTION * delta)
```
`move_toward(a, b, step)` moves `a` toward `b` by at most `step`, never overshooting.

**Starter values:**
```
SPEED = 200   ACCEL = 1200   FRICTION = 1000
```
**Time to reach full speed** = `SPEED / ACCEL` = 200/1200 ≈ **0.17 s**.
**Stopping distance** = `v² / (2·FRICTION)` = 200²/2000 = **20 px**.

Higher accel and friction means snappier. Lower means icy or heavy. **Air control:** use smaller ACCEL in the air (e.g. 60% of ground).

### 1.3 Exponential decay (alternative to linear friction)
```gdscript
velocity.x = lerp(velocity.x, dir * SPEED, 1.0 - exp(-10.0 * delta))
```
Smooth and frame-rate independent. The `10.0` is the "snappiness". Never use plain `lerp(a, b, 0.1)` per frame, because it behaves differently at 30 vs 144 fps.

### 1.4 Flip sprite
```gdscript
if dir != 0: sprite.flip_h = dir < 0
```

---

## 2. Gravity and falling

```gdscript
if not is_on_floor():
    velocity.y += GRAVITY * delta
velocity.y = min(velocity.y, MAX_FALL_SPEED)    # terminal velocity
```

**Why cap it?** Without a cap, long falls get so fast you tunnel through thin floors and can't react.

**Starter values:** `GRAVITY = 1200`, `MAX_FALL_SPEED = 600`.

---

## 3. Jump math (the most important section)

### 3.1 Derive gravity and jump speed from design numbers

Don't guess numbers. Decide **how it should feel**, then calculate.

Choose:
- `h` = jump height in px
- `t` = time to reach the peak in seconds

Then:

```
GRAVITY       = 2h / t²
JUMP_VELOCITY = -2h / t          (negative = up)
```

**Example:** h = 96 px (about 3 tiles of 32 px), t = 0.4 s
```
GRAVITY       = 2·96 / 0.16 = 1200
JUMP_VELOCITY = -2·96 / 0.4 = -480
```

**Check with the other formula:** `h = v² / (2g)` = 480²/2400 = 96 ✓

```gdscript
const JUMP_HEIGHT = 96.0
const TIME_TO_PEAK = 0.4
var gravity = 2.0 * JUMP_HEIGHT / pow(TIME_TO_PEAK, 2)
var jump_velocity = -2.0 * JUMP_HEIGHT / TIME_TO_PEAK
```

**Horizontal jump distance** (flat ground) = `SPEED × total air time` = `SPEED × 2t`. At SPEED = 200 and t = 0.4, that's 200 × 0.8 = **160 px**. **This number is the foundation of level design**: gaps wider than 160 px are impossible.

### 3.2 Variable jump height (hold = high, tap = short)
```gdscript
if Input.is_action_just_pressed("jump") and is_on_floor():
    velocity.y = jump_velocity

if Input.is_action_just_released("jump") and velocity.y < 0:
    velocity.y *= 0.4          # cut the rise short
```

### 3.3 Better fall feel (heavier falling)
```gdscript
var g = gravity
if velocity.y > 0:             # falling
    g *= 1.6
velocity.y += g * delta
```
Real jumps feel "floaty" with symmetric gravity. Heavier falling makes them snappy. This is why Mario feels good.

### 3.4 Coyote time (jump just after leaving a ledge)
```gdscript
var coyote = 0.0
# every physics frame:
if is_on_floor(): coyote = 0.1
else: coyote -= delta

if jump_pressed and coyote > 0:
    velocity.y = jump_velocity
    coyote = 0
```

### 3.5 Jump buffer (press jump slightly before landing)
```gdscript
var buffer = 0.0
if Input.is_action_just_pressed("jump"): buffer = 0.1
else: buffer -= delta

if buffer > 0 and (is_on_floor() or coyote > 0):
    velocity.y = jump_velocity
    buffer = 0; coyote = 0
```
Both timers are just **elapsed vs. duration comparisons** from your cooldown notes. 0.08 to 0.15 s is the sweet spot. Players never notice them, but they notice when they're missing.

### 3.6 Double jump
```gdscript
var jumps_left = 2
if is_on_floor(): jumps_left = 2
if jump_pressed and jumps_left > 0:
    velocity.y = jump_velocity
    jumps_left -= 1
```
(Combine with coyote time carefully, since coyote jump should use up the first jump.)

---

## 4. Wall mechanics

**Wall slide:** limit fall speed while touching a wall.
```gdscript
if is_on_wall() and velocity.y > 0:
    velocity.y = min(velocity.y, WALL_SLIDE_SPEED)    # e.g. 80
```

**Wall jump:** push away from the wall using its **normal**.
```gdscript
if is_on_wall_only() and jump_pressed:
    var n = get_wall_normal()                 # points away from wall
    velocity = Vector2(n.x * 250, jump_velocity)
```
The normal is a unit vector perpendicular to the surface. For a wall on your right, `n = (-1, 0)`, so you're pushed left.

**Tip:** briefly (~0.15 s) ignore horizontal input after a wall jump, otherwise you hold toward the wall and stick back to it.

---

## 5. Dash

```gdscript
# on dash press:
dash_time = 0.15
dash_dir = Vector2(facing, 0)

# in physics:
if dash_time > 0:
    dash_time -= delta
    velocity = dash_dir * DASH_SPEED       # e.g. 600
    # skip gravity while dashing
```
**Dash distance = DASH_SPEED × DASH_TIME** = 600 × 0.15 = **90 px**. Design it first, then back-calculate speed.
Add a **cooldown**: `dash_cd = 0.6`, count down by `delta`.

---

## 6. Collision math

### 6.1 AABB (axis-aligned bounding box)
Two rectangles overlap only if they overlap on **both** axes:
```
a.x < b.x + b.w  AND  a.x + a.w > b.x  AND
a.y < b.y + b.h  AND  a.y + a.h > b.y
```
Godot does this for you with `CollisionShape2D` (RectangleShape2D). Know the math anyway. It's the idea behind everything.

### 6.2 Slide response and floors
`move_and_slide()` removes the part of velocity that goes **into** the surface and keeps the part **along** it. That's **vector projection** in action.

**Is this surface a floor, wall, or ceiling?** Compare its normal with UP using the **dot product**:
```
normal.dot(Vector2.UP) > cos(floor_max_angle)   → floor
```
Godot's default `floor_max_angle` is 45° (cos 45° ≈ 0.707). Steeper than that counts as a wall.

### 6.3 Stomp detection (Mario-style)
Player stomps an enemy if they hit it **from above while falling**:
```gdscript
if velocity.y > 0 and player.global_position.y < enemy.global_position.y:
    enemy.die()
    velocity.y = -300        # small bounce
else:
    player.take_damage()
```
Or use the collision normal: `collision.get_normal().y < -0.7` means you hit its top.

### 6.4 Bounce pads and bouncing objects
```gdscript
velocity = velocity.bounce(normal)        # perfect reflection
velocity *= 0.8                           # energy loss (restitution)
```
Reflection formula: `v' = v − 2(v·n)n`. For a bounce pad, simply set `velocity.y = -BOUNCE_FORCE`.

### 6.5 One-way platforms
Enable **One Way Collision** on the platform's `CollisionShape2D`. Drop through by briefly disabling the collision mask or moving the player down a few pixels.

---

## 7. Enemies and hazards

### 7.1 Patrol enemy (walk back and forth)
```gdscript
velocity.x = direction * SPEED
if is_on_wall() or not ground_ray.is_colliding():
    direction *= -1
```
`ground_ray` is a `RayCast2D` pointing down-forward. If it stops hitting ground, there's a ledge ahead, so turn around.

### 7.2 Smooth back-and-forth with sine
```gdscript
t += delta
position.x = start_x + sin(t * SPEED_FACTOR) * RANGE
```
Good for flying enemies and moving saws. `sin` gives natural ease at both ends.

### 7.3 Projectiles
Straight: `position += dir * SPEED * delta`
Arced (grenade): treat it like the player. Give it initial velocity, then `velocity.y += gravity * delta`.

### 7.4 Knockback on damage
```gdscript
var away = (global_position - enemy.global_position).normalized()
velocity = Vector2(away.x * 250, -200)
```
Use `normalized()` so knockback strength doesn't depend on distance. Add **i-frames** (0.8 s invincibility) using a timer.

---

## 8. Moving platforms
Platform moves with sine or a `Tween`. In Godot 4, put it on an `AnimatableBody2D`, and `CharacterBody2D` automatically inherits platform velocity while standing on it.
**Design number:** platform speed must be less than the player's speed, or the player can't catch up when jumping from one to the next.

---

## 9. Camera math

```gdscript
# Smooth follow: enable on Camera2D
position_smoothing_enabled = true
position_smoothing_speed = 6.0
```

**Look-ahead** (see further in the direction you're moving):
```gdscript
var target_offset = Vector2(facing * 60, 0)
offset = offset.lerp(target_offset, 1.0 - exp(-5.0 * delta))
```

**Limits:** set `limit_left`, `limit_right`, `limit_top`, `limit_bottom` so the camera stops at level edges.

**Screen shake** (on hit or land):
```gdscript
trauma = max(trauma - delta * 1.5, 0.0)
offset = Vector2(randf_range(-1, 1), randf_range(-1, 1)) * trauma * trauma * 12.0
```
Squaring trauma makes small shakes subtle and big ones violent.

---

## 10. Parallax
Each background layer moves at a fraction of the camera's speed:
```
layer_offset = camera_x * motion_scale
```
Far layers scale `0.1` to `0.3` (barely move), near layers `0.6` to `0.9`. Use Godot's `Parallax2D` node and set its scroll scale.

---

## 11. Easing and juice (cheap feel upgrades)

**Squash and stretch** with a tween:
```gdscript
var t = create_tween()
t.tween_property(sprite, "scale", Vector2(1.3, 0.7), 0.05)   # land squash
t.tween_property(sprite, "scale", Vector2.ONE, 0.15).set_trans(Tween.TRANS_BACK).set_ease(Tween.EASE_OUT)
```
Easing = shaping how a value changes over time (ease-in slow start, ease-out slow end, back/bounce for overshoot).

---

## 12. Level design math (this is where it pays off)

Compute the player's limits **once**, then build levels inside them:

```
Max jump height     = h                  (96 px  = 3 tiles)
Max jump distance   = SPEED × 2t         (160 px = 5 tiles)
Safe gap            ≈ 80% of max         (128 px = 4 tiles)
Safe platform rise  ≈ 80% of max height  (2 tiles)
```
With a 32 px tile, think in tiles. **Never design a required jump at 100% of your max.** Leave margin for human error.

---

## 13. Summary: platformer math map

| Mechanic | Math | Godot tool |
|---|---|---|
| Run | accel / friction, `move_toward` | `velocity.x` |
| Gravity | `v += g·dt`, terminal cap | `velocity.y` |
| Jump | `g = 2h/t²`, `v = -2h/t` | `CharacterBody2D` |
| Variable jump | velocity cut, gravity multiplier | `just_released` |
| Coyote / buffer | timers (elapsed vs duration) | float counters |
| Wall jump | surface normal, push vector | `get_wall_normal()` |
| Dash | `distance = speed × time` | timer |
| Floor check | dot product vs. UP | `is_on_floor()` |
| Stomp | normal / position compare | `get_normal()` |
| Bounce | reflection `v − 2(v·n)n` | `bounce()` |
| Patrol | sign flip, sine | `RayCast2D` |
| Knockback | normalized direction | `normalized()` |
| Camera | exponential smoothing, trauma² | `Camera2D` |
| Parallax | layer speed ratio | `Parallax2D` |
| Level design | jump height and distance limits | TileMap |

---

## Practice build (rectangles only)

1. Scene `Player`: `CharacterBody2D` root, child `ColorRect` (32×48, blue), child `CollisionShape2D` (RectangleShape2D 32×48).
2. Scene `Level`: `Node2D` root, 3 `StaticBody2D` children, each with a `ColorRect` and a matching `CollisionShape2D` (one floor, two platforms).
3. Implement in this order, testing after each: **run → gravity → jump from `h` and `t` → variable jump → coyote → buffer**.
4. Place the platforms using the **safe gap** numbers from section 12, then check you can cross them reliably.

---

**Top-down math notes: not started.** We'll write those when we begin a top-down game.
