# Input Functions: get_axis vs is_action_pressed

> **Source:** Day4/1_doubts.md (Doubt 3)  
> **Plan day:** Day 2

---

# Doubt 3: All about Input functions
## `Input.get_axis(negative_action, positive_action)`

**What it does:** Checks two opposing actions and gives you back a single float between -1 and 1 representing the net direction.
- Only negative action pressed → `-1.0`
- Only positive action pressed → `1.0`
- Neither pressed → `0.0`
- Both pressed at once → `0.0` (they cancel out)

**When to use it:** Any time a movement or value is naturally a *pair of opposites* — left/right, up/down, forward/backward, throttle/brake. Basically anywhere you'd otherwise write "if left pressed do -1, if right pressed do +1."

**Why use it:** It's one function call instead of two `if` checks, it automatically handles the both-pressed-cancel-out case for you, and — this is the big one — it's built to also work seamlessly with analog input (gamepad stick, trigger pressure) later, returning values like `0.35` instead of just -1/0/1. Writing your own version with `is_action_pressed()` would only ever give you the digital -1/0/1 case unless you specifically account for analog.

**How to use it:**
```
var x := Input.get_axis("ui_left", "ui_right")
var y := Input.get_axis("ui_up", "ui_down")
var direction := Vector2(x, y)
```
You pass it two *action name strings* (the same names you'd see in Project Settings → Input Map), and it returns the float. Combine two axis calls (X and Y) into a `Vector2` to get full 2D direction.

---

## `Input.is_action_pressed(action_name)`

**What it does:** Checks a single action and returns `true` if it's currently held down, `false` otherwise. No pairing, no float — just a boolean for one specific input.

**When to use it:** Any action that *doesn't* have a natural opposite — Jump, Kick, Plunge, Interact, Pause. There's no "negative Jump" to pair it with, so `get_axis()` doesn't apply; you just need to know "is this one button down right now."

**Why use it:** It's the right tool when you genuinely only care about one binary state, not a spectrum between two opposites. Using `get_axis()` for something like Jump would be forcing a two-action pattern onto something that's inherently one action — unnecessary and confusing.

**How to use it:**
```
if Input.is_action_pressed("ui_accept"):
    # jump logic here
```
Note: `is_action_pressed()` is true for *every frame* the key is held — for a one-shot action like Jump where you only want it to fire once per press, you'll actually want `Input.is_action_just_pressed()` instead (fires true only on the exact frame the key goes down). That distinction becomes relevant on Day 3 when you build Jump.

## Doubt: What happens when Left and Right are pressed at the same time?

**Short answer:** Nothing random happens — the result is deterministic and predictable, every time.

**Why:** `get_axis(negative_action, positive_action)` is essentially computing:
```
float(is_action_pressed(positive_action)) - float(is_action_pressed(negative_action))
```
Each boolean converts to `1.0` (pressed) or `0.0` (not pressed). Press both Left and Right together → `1.0 - 1.0 = 0.0`, every single time. Same inputs always produce the same output — that's what "deterministic" means. Think of it like tug-of-war: equal force from both ends nets zero movement, not a random direction.

**The precision that matters:** this cancellation only zeroes out **that one axis** — it doesn't freeze the character entirely. X and Y are computed independently via two separate `get_axis()` calls. So holding Left+Right (X cancels to 0) while also holding Up (Y = -1) still moves the Ostrich straight up — only the conflicting axis goes neutral, the other keeps working normally.

**Takeaway:** `get_axis()` is for opposing action pairs. Both pressed at once → that pair's contribution becomes zero, other axes unaffected. This is the same "what happens when two things compete" question you'll revisit on Day 3, when Jump and Plunge inputs can potentially overlap.

