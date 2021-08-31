---
layout: default
title: Collision Edge Cases
nav_order: 4
parent: Platformer | Movement
---

# Collision Edge Cases

Computers have a pesky way of doing exactly what we tell them to do, but sometimes that's not exactly what we mean. **Edge Cases** are uncommon scenarios which break our logic, but despite being uncommon, we still need to fix them

## Stutter Landing

If you play it long enough, you might notice some weird issues if you hit the ground at high speed. I'll call this a stutter landing

![](../../images/platformer/stutter_landing.gif)

Here's a frame by frame of what's happening

0: moving fast
1: still moving fast, but next frame will clearly collide
2: dy = 0
3: starts accelerating again
...

**TODO** represent the above as an image
![](../../images/platformer/stutter_landing_frames.png)

So on frame 1 we're moving fast, and it looks like we're about to hit the ground, but instead on frame 2, it just stays in place and the dy is set to 0. Then gravity starts accelerating the dy again and, since the dy is much smaller, we have space to move again

This is exactly what we're telling the game to do, when we detect that a collision is about to happen we just set ``dy = 0;`` we don't tell it keep moving until it hits the ground. So instead it keep continuously colliding with the wall, and then approaching at slower and slower speeds until it's up against the wall

To fix this we would use ``move_contact_solid()``, it moves our instance in a direction until it touching a solid

```
// oPlayer Step Event
//// VERTICAL
{
    // gravity
    // jumping
    // collisions
    if(not place_free(x, y+dy)){
        if(dy > 0){
            move_contact_solid(270, abs(dy));
        }
        if(dy < 0){
            move_contact_solid(90, abs(dy));
        }
        dy = 0;
    }
    // update y
}
```

So now, if we're about to hit something, we first keep moving till we actually collide, and only then do we stop moving

``270/90``: The first parameter is which direction to move in. So when we're going down (i.e. ``dy > 0``), we use the downward direction ``270``, otherwise we use the upward direction of ``90``

``abs(dy)``: The second parameter is the max distance to move. It should be impossible for us to move further than our ``dy`` (since according to ``place_free`` there's definitely a collision), but just to be safe I'm setting ``dy`` as our maximum. We could also just put ``-1`` (or any other negative number) to indicate no maximum (meaning it'll keep going infinitely), but I figure it's safer to go with a reasonable maximum just in case. I use ``abs()`` because move_contact expects a positive number, and I don't want it to think I mean an infinite distance

## Corner Case

There's another weird scenario that can occur if you hit a corner in just the right way. I already have the code written to avoid this, but I wanted to go over it anyway just so you're aware

With flappy bird, I updated the x and y position at the same time as follows

```
x += dx;
y += dy;
```

but I didn't do that here. Let's dig into why

Let's say we're moving with ``dx = 8;`` and ``dy = 8;`` (yes I know I'm not using ``dx`` in the current code, but I could if I wanted to) and then we hit a corner. Here's one way I could apply the movement

1. Check Horizontal Collisions
2. Check Vertical Collisions
3. Apply ``dx`` to ``x``
4. Apply ``dy`` to ``y``

Here's how that would look

![](../../images/platformer/corner_case1.png)

So we checked both horiztonal and vertical collisions, but the problem is that if you're hitting a corner, the collision will only happen if you check BOTH horitzontal and vertical at the same time. Here's an alternate approach (the one I used in the code), and notice how this one works better

1. Check Horizontal Collisions
2. Apply ``dx`` to ``x``
3. Check Vertical Collisions
4. Apply ``dy`` to ``y``

![](../../images/platformer/corner_case2.png)

So now we're still checking for both collisions separately, but since we're updating our x position before checking vertical collisions we ensure that the collision check is using the most up to date info
