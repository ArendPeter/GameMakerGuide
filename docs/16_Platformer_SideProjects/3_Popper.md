---
layout: default
title: Popper
nav_order: 3
parent: Flappy Bird | Side Projects
---


## Where do I start?

<details markdown="1">

<details data-summary="How do I implement the shooting?" markdown="1">
1. Creates objects for ``oBalloon``, ``oArrow``, ``oWall``, as well as their corresponding sprites
1. Update ``oBalloon`` to start with a random color
1. Setup ``oBalloon`` to move using ``dx`` / ``dy``, and it should start off with both set to 0
1. Make ``oArrow`` point toward the mouse
1. Update ``oArrow`` to spawn a non-moving ``oBalloon`` at the beginning, and then set it's ``dx`` / ``dy`` when the mouse is pressed
1. Update ``oArrow`` to reload the ball
1. Update ``oBalloon`` to bounce off of walls
</details>

<details data-summary="How do I implement the balloon vs balloon interactions?" markdown="1">
These are tricky, but don't worry there will be follow up FAQs
1. When a moving ``oBalloon`` collides with another ``oBalloon``, make it stop, and snap to align with the grid
1. After it checks it should check the ``oBalloon`` around it for matching colors, and if there are matches, then pop! 🎉
</details>

</details>

## How do I make the balloons bounce in a generic way?

<details markdown="1">
We actually had this question back when making breaker as well. The quickest way is to use ``move_bounce_all(false);``, and that's perfectly fine, but you can also code it yourself. Here's the code I linked to back in breaker (but updated to include some of the syntax we've learned since then)

```
var other_wall = instance_place(x+dx, y+dy, oWall);
if(other_wall != noone){
	// I get the diff to see if we're further apart horizontally, or vertically
	var x_diff = abs(x - other_wall.x);
	var y_diff = abs(y - other_wall.y);

	// these represent the distance between the 2 centers when a horizontal or vertical collision happens
	var hor_col_dist = abs((sprite_width + other_wall.sprite_width) / 2);
	var vert_col_dist = abs((sprite_height + other_wall.sprite_height) / 2);

	// I'm applying scaling to keep the logic accurate for wide shapes
	// ex. if the ball hits the left side of the paddle, x_diff will be bigger than y_diff, but it's a vertical collision
	// So for a horizontal collision, the scaled_x_diff should be 1, and scaled_y_diff should be guarenteed to be less than 1 (hence scaled_x_diff > scaled_y_diff), also vice versa
	var scaled_x_diff = x_diff / hor_col_dist;
	var scaled_y_diff = y_diff / vert_col_dist;

	// horizontal collision
	if(scaled_x_diff >= scaled_y_diff){
		dx = (x < other_wall.x)? -abs(dx) : abs(dx);
	// vertical collision
	}else{
		dy = (y < other_wall.y)? -abs(dy) : abs(dy);
	}
}
```

This script gets kind of weird if you hit a wall exactly at a corner. It'll trigger twice, and the speed will be mirrored along both dimensions. To minimize the corner cases, you can stretch the walls in the room instead of duplicating them

> **Why go through the effort to code it myself, when Game Maker already has it for me?**: Because it's more fun🤓! Sometimes I dive into coding it from scratch by default, but that's a bad habit. The more time I spend on reinventing the wheel, the longer it will take to complete my game. BUT there will definitely be times where you're forced to do things from scratch because your requirements are slightly different, so it's a good habit to build up. For example, understanding this script, will be very helpful, when you move on to making baloons stick together. My recommendation is to code from scratch whenever the inspiration hits you while you're in the learning process, but once you're more advanced, you should avoid reinventing the wheel wherever possible

</details>

## How do I make it the balloons stick together?

<details markdown="1">
There are a couple of approaches. In my case I checked if it was a horizontal or positive collision, and then snapped the balloon to ``(x+64, y)``, ``(x-64, y)``, ``(x, y-64)``, ``(x, y+64)`` accordingly

To determine the horizontal vs positive collision, check out the bounce code, and see if you can update it for this scenario

it doesn't lock to the one that I think it should (to fix this we'd probably need to use instance_place_list)

</details>

## Ahhgh, all of the balloons move when collisions happen!!

<details markdown="1">
Yep, I hit this too. It's a catastrophically funny bug (<span id="years-of-game-maker"></span> years of Game Maker and there are still surprises)

![](../../images/platformer/popper.gif)

For me this was happening because all my baloons were checking for collisions with other balloons. That meant that the collision logic was triggered for 2 balloons when the *bullet* balloon hit the [herd](https://www.answers.com/Q/What_is_a_group_of_balloons_called), and when the non-moving baloon updated it's position it triggered a chain reaction of collisions with other baloons

</details>

## How do I make all the baloons in a group pop?

<details markdown="1">

I implemented the popping by doing checks on all sides for adjacent balloons, and then if the colors matched, I'd set an alarm in those adjacent balloons to do a similar pop check on it's neighbors

After doing the checks, I'd delete the current balloons

</details>

## The wrong baloons are popping

<details markdown="1">

I mentioned how I implemented the pop check to make balloons pop in a group. After implementing it, I hit a lot of issues with the wrong balloons being triggered

Turned out it was because I was using ``instance_place(x, y, obj)`` to retrieve the neighboring balloons, and it was finding multiple

When ``instance_place(..)`` detects multiple instance, it essentially ranomdly selects which instance to return (not technically random, but it's unpredicable). I tried adjusting the collision masks to ensure that ``instance_place(..)`` only detected one, but it didn't work

We could solve this using ``instance_place_list(..)``, but that's kind of complicated. For me I managed to fix this issue by switching to ``instance_point()``. It's a very similar function but it made all my problems go away

</details>

## My baloon pattern is the same every time!

<details markdown="1">

This is because Game Maker's random functions aren't truely random. It start's with a seed, and then generates the same sequence of numbers whenever you run the game. This makes debugging easier, but it doesn't offer a lot of variety

To solve this, just make sure to call ``randomize()`` before you use any other random functions (if we had an ``oControl`` object I'd say do it there, but it doesn't really matter)

</details>
