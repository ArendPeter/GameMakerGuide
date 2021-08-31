---
layout: default
title: Horizontal Movement
nav_order: 1
parent: Platformer | Movement
---

# Horizontal Movement

## Resourse Setup

Start by loading in the sprites for the project, and then create objects for ``oGround``, and ``oPlayer``

For ``oPlayer`` you can use ``sPlayerIdle`` for the sprite. For the ``oGround`` use ``sGround``, and make sure that you check the box for solid (we'll go over this soon)

![](../../images/platformer/ground_solid.png)

Then populate your room, here's what I came up with

![](../../images/platformer/initial_room.png)

## Basics

Let's start with a simple left/right. This will use the same technique as pong, but apply the keyboard functions that we learned for flappy bird

```
// oPlayer Create Event
max_dx = 8;

// oPlayer Step Event
//// HORIZONTAL
{
    if(keyboard_check(vk_left)){
        x -= max_dx;
    }
    if(keyboard_check(vk_right)){
        x += max_dx;
    }
}
```

When you test it out, you'll notice that we can move left/right, but we can also go through walls, let's fix that next

![](../../images/platformer/horizontal_no_collision.gif)

## Collisions

Currently our condition only checks for the keyboard button, which is kind of like asking "do we have the will to move". But as we all know, having the will isn't enough, we need to have a will AND a way. So let's update the code to also check for collisions before moving

```
// oPlayer Step Event
//// HORIZONTAL
{
    if(keyboard_check(vk_left) and place_free(x-max_dx, y)){
        x -= max_dx;
    }
    if(keyboard_check(vk_right) and place_free(x+max_dx, y)){
        x += max_dx;
    }
}
```

``and``: We mentioned this operator back in the mobile porting section. It combines 2 booleans and only returns true if both are true. That makes sense for this case, we only want to move if we're both pressing the button, and there's space available

``place_free(x-max_dx, y)``: ``place_free()`` is a function which checks if we would be colliding with any solids if we moved to a given position. So I give it the position I'm about to move to, and then it returns true if I wouldn't be colliding with any solids

``solid``: Earlier we explicitly marked ground as a solid so that it'll be monitored by ``place_free()``. The solid option gives us a way to say which items don't want to overlap with. For example if we made a coin solid, then the player wouldn't be able to collect them because our collision logic would keep us from walking into the coin

We've used ``place_meeting()`` in the past, be careful not to mix it up with ``place_free()``. The ``place_`` are probably the most popular collision functions, so here are some definitions to help you keep them straight

| function | definition |
|---|---|
| place_meeting(x, y, object/instance) | returns true if moving to the given position would trigger a collision with the given object or instance |
| place_free(x, y) | returns true if no solid collisions would be detected by moving to the given position |
| place_emtpy(x, y) | returns true if no collisions would be detected by moving to the given position |

So ``place_free()`` and ``place_empty()`` are identical, except ``place_free()`` only checks for solids where as ``place_empty()`` will check for both solids and non-solids

> **Wait but aren't I though we used other collision functions before?**: You're right 🤓, for example we used ``position_meeting()`` when we were coding up our buttons. place is just one prefix, and that represents checking if the current instance would collide that the given position, but there are other prefixes. ``position_meeting()`` checks only a given position instead of using the collision box of the current instance. ``instance_place()`` is similar to ``place_meeting()`` except that it gives you back the instance id of the colliding instance, rather than just a boolean. ``collision_line()`` let's you define a line and it'll tell you if anything collides with that line. But all that said, I'd say the ``place_`` vartiations are the most common, so you can stick to those for now

When we test it out, our horizontal collisions should be working 😁

![](../../images/platformer/horizontal_with_collision.gif)

## WASD

Let's extend our boolean logic brains and try adding WASD support as well. How about you give it a try? (and when you do be sure to test that both WASD and left/right handle collisions properly)

<details data-summary="Can you add WASD support?" markdown="1">

We're gettting into some more complicated boolean logic now,

```
// oPlayer Step Event
//// HORIZONTAL
{
    if((keyboard_check(vk_left) or keyboard_check(ord("A"))) and place_free(x-max_dx, y)){
        x -= max_dx;
    }
    if((keyboard_check(vk_right) or keyboard_check(ord("D"))) and place_free(x+max_dx, y)){
        x += max_dx;
    }
}
```

``ord("A")``: I've mentioned this before, but it's worth repeating, that there isn't a vk_A constant in Game Maker. For letter keys you need to use the ``ord()`` function and you need to make sure that the letters are upper case

The most important part of this statement is the added parentheses. Just like with math we can use these to indicate the order of the operations, and here we explictly want to ensure we handle our keyboard inputs as a group, otherwise the and we'll be computed first and we get problems. In english we kind of use commas for the same purpose

```
move if we're holding left  or we're holding A, and there's space free
move if we're holding left, or we're holding A  and there's space free
```

You can see in the second line we can move when holding left, regardless of whether there's space free. If you remove the parenthesis from the code you'll see the same behaviour in game

</details>

## Mirroring

Needless to say, the game looks kind of ugly right now. In the next sections we'll fix that by applying more of the art, but until then we can make the player face the direction it's moving as a quick win

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
