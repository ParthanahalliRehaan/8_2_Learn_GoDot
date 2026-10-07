# Math: get_axis Range vs World Position

> **Source:** Day4/1_doubts.md (Doubt 6)  
> **Plan day:** Day 2

---

# Doubt 6: Maths
## Doubt 6: Does get_axis()'s -1 to 1 range mean the object can't move past those bounds?

**Short answer:** No — that mix-up is worth untangling carefully, because it conflates two different things: **direction/input value** vs **actual position in the world**.

**What -1 to 1 actually represents:** `get_axis()` only tells you *which way* the input is pointing and how strongly (for analog sticks) — it says nothing about *distance traveled*. It's the `x` or `y` component of your `direction` vector, not a position or a distance.

**Where speed and time come in:** Your actual velocity is `direction * speed`. If `direction.x = 1.0` and `speed = 300.0`, then `velocity.x = 300.0` — pixels per second, not "1 pixel total." Position then accumulates every physics frame via `move_and_slide()` (internally doing `position += velocity * delta` each tick). Over 5 seconds of holding right, you've moved `300 * 5 = 1500` pixels — nowhere close to being capped at "1."

**Analogy:** think of `get_axis()` as a steering wheel, not an odometer. Turning the wheel fully right gives you `1.0` — that doesn't mean the car can only travel 1 meter, it means "steer fully right," and how far you actually go depends on how long you hold the gas (⇔ how many physics frames pass) and how fast the engine can go (⇔ `speed`).

**One genuine subtlety worth flagging (relates to your normalization doubt earlier):** the -1 to 1 range *does* cap the raw `direction` vector's components, which is exactly why diagonal movement (direction like `(1, 1)`, magnitude ≈1.41) moves faster than straight movement (direction like `(1, 0)`, magnitude 1) unless you call `.normalized()`. So the cap affects *direction magnitude*, which indirectly affects speed consistency — but never position, which is unbounded and just keeps accumulating over time.

**Test it yourself:** hold Right for 1 second vs 5 seconds in your running scene — the Ostrich should travel roughly 5x farther in the second case, confirming position isn't capped by that -1/1 range at all.

