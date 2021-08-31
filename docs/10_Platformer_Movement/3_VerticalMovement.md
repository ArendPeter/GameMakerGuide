---
layout: default
title: Vertical Movement
nav_order: 3
parent: Platformer | Movement
---

# Vertical Movement

We've got horizontal working, now let's try vertical. We'll reuse the gravity techniques we learned from flappy bird, but we also need to add some new stuff for collisions to work properly

## Gravity

```
// oPlayer Create Event
dy = 0;
grav = .5;

// oPlayer Step Event
//// HORIZONTAL
//// VERTICAL
{
    // gravity
    dy += grav;

    // update y
    y += dy;
}
```

There it is, it should be the same as flappy bird

When we test it out the player should fall, but she'll also go straight through the ground

![](../../images/platformer/gravity_no_collision.gif)

## Collisions

At the moment, we fall through the ground, so let's add collisions

```
// oPlayer Step Event
//// VERTICAL
{
    // gravity
    // collisions
    if(not place_free(x, y+dy)){
        dy = 0;
    }
    // update y
}
```

``not place_free``: ``place_free`` tells us if moving to a position will be free of collisions, so if we want to check if moving a position will not be free, we just add the ``not``

``(x, y+dy)``: So we've set dy in the gravity section, but we're not sure if that dy will work yet. So this if statement is to check if we're about to hit something so that we can apply the breaks. For this reason it's important to include the ``+dy``

``dy = 0;``: If there's no space, then we apply the breaks

![](../../images/platformer/gravity_collision.gif)

## Jumping

Now let's try jumping 🦘

```
// oPlayer Step Event
//// VERTICAL
{
    // gravity
    // jumping
    if(keyboard_check_pressed(vk_up) and not place_free(x, y+1)){
        dy = -10;
    }
    // collisions
    // update y
}
```

Since jumping is updating ``dy``, it's important that we do the jumping logic before the collision check. Otherwise we might update ``dy`` and then go through a wall because we forgot to check whether to break

``keyboard_check_pressed(vk_up)``: I'm using pressed here because I only want the jump to be executed when we initially press space, not every frame that it's held

``not place_free(x, y+1)``: I also want to make sure we have something to jump off of. I'm using the same strategy as before to check for something under us, but I'm only adding 1 to check if we're directly on top of it

Yay, now our platformer looks pretty good, but there are still some weird scenarios that can occur

![](../../images/platformer/jumping.gif)
