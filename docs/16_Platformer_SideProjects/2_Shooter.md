---
layout: default
title: Shooter
nav_order: 2
parent: Platformer | Side Projects
---

# Shooter

## How do I start?

<details markdown="1">
1. Make objects for ``oPlayer``, ``oLargeAsteroid``, ``oSmallAsteroid``, ``oControl``, ``oBullet``, as well as the corresponding sprites
1. Update ``oPlayer`` to move in all 4 directions
1. Update ``oPlayer`` to angle toward the mouse and shoot ``oBullet`` objects on mouse click
1. Update ``oLargeAsteroid`` to start with a random(ish) rotation speed, direction, and speed
1. Update ``oControl`` to spawn ``oLargeAsteroid`` objects along the edge of the screen
1. Update ``oLargeAsteroid`` to explode into the smaller asteroids when it collides with ``oBullet``
1. Update ``oSmallAsteroid`` to award points when it collides w/ ``oBullet``
1. Store & display a persistent high score across game runs
1. Add a paritcle effect to the small asteroid death
</details>

## My player moves faster when it's going diagonally!

<details markdown="1">
Previously we'd have a variable ``max_speed``, and then we set ``dx`` or ``dy`` to that value depending on the direction that we're moving

If they're both at ``max_speed`` then their combination results in a higher speed, and that looks kind of weird

There are a couple ways to fix this, one is to chain a bunch of if statements to handle all the combinations (``if(left and not up and not down)``, ``if(right and not up and not down)``, etc)

One of the better ways, imo, is to leverage polar as follows

```
var input_x = keyboard_check(vk_right) - keyboard_check(vk_left);
var input_y = keyboard_check(vk_down) - keyboard_check(vk_up);

var input_dir = point_direction(0, 0, input_x, input_y);

dx = lengthdir_x(max_speed, input_dir);
dy = lengthdir_y(max_speed, input_dir);
```

Here I combine the booleans from the keyboard functions (remember boolean is just ``0``, or ``1``, so doing math on them is fine). This gives us ``-1``, ``0``, ``1`` for each

From there we can get a direction (``point_direction()`` is just math, it doesn't care if you're giving it actual room coordinates). For example, if you're going up and to the right, we get ``1`` and ``-1`` for ``input_x`` and ``input_y``. Then if you run ``point_direction(0, 0, 1, -1)`` you get ``45``. Voila 🥐!!

</details>

## How do I spawn the asteroids?

<details markdown="1">
I want them to start outside the room, and I can split that into to cases. top/bottom or, left/right

For top/bottom, I can use ``random_range(-64, room_width+64)`` for ``x`` and ``choose(-64, room_height+64)`` for ``y``, and I can do something similar for left/right
</details>

## How do I make it more difficult over time?

<details markdown="1">
I spawn new asteroids in on ``oControl`` alarm event, and then in the alarm event and reset the alarm so that it will retrigger later

I have a variables, let's say spawn_steps, that determines the new value when reseting the alarm

So we just need to decrease that over time, for me I put ``spawn_steps *= .999;`` in the step to reduce it over time

WARNING: At first I put ``spawn_steps *= .8`` in the alarm event, but that was really weird. Since it was reducing more often as the alarm was triggered more often there was a point where it would reduce super fast, and all of a sudden I had a thousand asteroids on the screen 😬 (totally worth a try for the experience though)
</details>

## I want the space ship to be more "floaty"? Since it's space?

<details markdown="1">
Ah, you're talking about friction. Flying in space is a lot like walking on ice, when you try to stop moving it takes a while to slow down. The ground has high friction which slows, you down, space and ice have low friction

I'll use the code from the diagonal movement question as a starting point

```
var input_x = keyboard_check(vk_right) - keyboard_check(vk_left);
var input_y = keyboard_check(vk_down) - keyboard_check(vk_up);

var input_dir = point_direction(0, 0, input_x, input_y);

dx = lengthdir_x(max_speed, input_dir);
dy = lengthdir_y(max_speed, input_dir);
```

here's how you make it use friction

```
var input_x = keyboard_check(vk_right) - keyboard_check(vk_left);
var input_y = keyboard_check(vk_down) - keyboard_check(vk_up);

var input_dir = point_direction(0, 0, input_x, input_y);

dx += lengthdir_x(max_acc, input_dir);
dy += lengthdir_y(max_acc, input_dir);

dx *= fr;
dy *= fr;

var sp = point_distance(0, 0, dx, dy);
if(sp > max_sp){
	dx *= max_sp / sp;
	dy *= max_sp / sp;
}
```

``dx += lengthdir_x(max_acc, input_dir);``: 2 subtle differences here. First I use ``+=`` instead of ``=``. This means we're adding acceleration to the previous speed, rather than directly setting the speed. Also I updated ``max_sp`` to be ``max_acc`` since it now represents acceleration

``dx *= fr;``: ``fr`` is a variable between ``0`` and ``1``. Here I make the speed smaller every step, that way if you stop moving, friction will slow you down over time. Something like ``.2`` will make the friction slow you down very quickly, where as ``.999`` would be very slow

``if(sp > max_sp){``: With the acceleration, the speed can get really crazy, so I still want to enforce a max. I used ``point_distance`` to get my current speed, then if its above ``max_sp``, I multiply both components by ``max_sp / sp`` to scale it. So if ``sp`` is double ``max_sp``, then ``max_sp / sp`` will end up multiplying both components by ``.5``

</details>

## How do I make the player wrap to the other side of the screen?

<details markdown="1">
Sure, just check for when the player is outside room and add or subtract the width or height of the room accordingly. I beleive in you 😀
</details>

## Can I use polymorphism here?

<details markdown="1">
Yes you can! I actually made both asteroids children of an ``oDeadly`` parent. That way, when checking for collisions ``oPlayer`` only needs to check for ``oDeadly`` instead of both asteroids. This will also extend nicely if you add more enemies to the game
</details>

## I want to more ideas!!

<details markdown="1">
Sure thing!

One adjustment I added is to make the ship constantly shoot instead of requiring mouse presses. Here's a Game Design rule of thumb: "For each of the player's abilities, there needs to be a reason to use the ability as well as a reason not to use the ability". So for example, in most games the reason you shoot is to kill things, and the reason not to shoot is to save your ammo. But our game doesn't have ammo, so why not just have the ship always shoot? (also my hand was starting to hurt, and I was tired of clicking)

</details>
