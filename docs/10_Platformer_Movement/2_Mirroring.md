---
layout: default
title: Mirroring
nav_order: 2
parent: Platformer | Movement
---

# Mirroring

Needless to say, the game looks kind of ugly right now. In the next sections we'll fix that by applying more of the art, but until then we can make the player face the direction it's moving as a quick win

## image_xscale

Remember ``image_xscale``? We used it to make the balls bigger back in the pong game. There's a nice trick you can do where using a negative ``image_xscale`` to make the image mirror. You can test this in the room

![](../../images/platformer/room_mirror_player_misaligned.gif)

Let's try updating the code to take advantage of this

```
// oPlayer Step Event
//// HORIZONTAL
{
    if((keyboard_check(vk_left) or keyboard_check(ord("A"))) and place_free(x-max_dx, y)){
        x -= max_dx;
        image_xscale = -1;
    }
    if((keyboard_check(vk_right) or keyboard_check(ord("D"))) and place_free(x+max_dx, y)){
        x += max_dx;
        image_xscale = 1;
    }
}
```

When we test it out we see the player faces the right direction, but there seems to be a weird jump whenever we switch directions

![](../../images/platformer/mirror_player_misaligned.gif)

## Alignment

Why is this happening? Well if you look back at the room editor example, you'll notice the same jump when the gif restarts. In the room editor the x/y isn't changing but the problem is that x/y sprite offset is set to the top left of the sprite, and when you mirror it that becomes the top left corner

Whenever you're mirroring sprites, the horizontal offset always needs to be in the center. On top of that I also like to set the vertical offset to the bottom. That way her feet will stay stuck to the ground in case we ever decide to scale it vertically

![](../../images/platformer/updated_player_alignment.png)

![](../../images/platformer/room_mirror_player_aligned.gif)

Now when we test it in game it looks much better 🥳

![](../../images/platformer/mirror_player_aligned.gif)

## Collision Masks

While we're working with the sprites, I figured we could talk a bit about collision masks. When I was talking about ``place_free()`` earlier I said it was checking whether the current instance would collide with anything at a given position, but how is it checking the collision exactly?

Maybe it's just checking if the sprites overlap? Well that can't be right. If we look at how our player collides you can see she can get very close to walls, clearly past the point where the sprites are overlapping. For the collisions, game maker is actually using the sprites collision mask, and not the whole sprite

**TODO** I'm thinking this image will have 3 parts, the player's sprite, a gameplay screen shot of the player against the wall, and the player's collision mask

![](../../images/platformer/collision_mask_comparison.png)

Automatic uses a rectanglular mask, but you can manually reshape the rectangle or even use a different shape entirely if you want

That said, the default rectangle is usually a pretty decent fit with the art so I usually just go with that

You'll inevitably hit a bug sometime in your career because of a weird collision mask (and I can say that for certain because we'll hit one later in this course 😉), so I wanted to make sure you're aware of the concept
