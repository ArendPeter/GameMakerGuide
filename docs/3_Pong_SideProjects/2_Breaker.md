---
layout: default
title: Breaker
nav_order: 2
parent: Pong | Side Projects
---

# Breaker

## Where do I start?

<details markdown="1">
Here's how I would break it down, it starts off very similarly to the previous one (and tbh, I used the previous project as a starting point)

1. Create ``sWall``, ``sBall`` and ``sPaddle`` sprites
1. Create sprites for the bricks (I named them according to their scores) ``sBrick1``, ``sBrick3``, ``sBrick5``, ``sBrick7``
1. Create corresponding ``oWall``, ``oBall``, and ``oPaddle`` objects, as well as for all the bricks
1. Make ``oPaddle`` move left/right
1. Make ``oBall`` move downward without acceleration
1. Add a collision between ``oPaddle`` and ``oBall`` to handle bounce (mine has aiming, but you can start with a basic collision)
1. Add a collision between ``oBall`` and ``oWall``, as well as ``oBall`` and eacho of the bricks. All for bounce
1. Use the outside room event to make ``oBall`` reset
1. Draw the points (I handled it from the ``oPaddle``, but you can do any object)
1. Update the ``oBall`` and ``oBrick`` collisions to also update poitns
1. Add a custom font for the points

</details>

## How do I make the ball bounce differently depending on where it hits the brick?

<details markdown="1">

When bouncing off walls, we'd first figure out if the wall was on the left, right, top, or bottom side of the screen, and then figure out the bounce logic accordingly. Here's how you'd handle those 4 cases

```
hspeed = abs(hspeed); // bounce to the right
hspeed = -abs(hspeed); // bounce to the left
vspeed = abs(vspeed); // bounce down
vspeed = -abs(vspeed); // bounce up
```

The bricks make this a bit trickier since a single brick can be hit from all sides. I thought of bounce as a fairly basic feature, but the logic for handling a general bounce like this is surprisingly complicated

Luckily Game Maker has a built in function for this, so you can add this line to your collision events instead

```
move_bounce_all(false);
```

**Caution**: I'll be teaching other movement techniques in the following sections, but ``move_bounce_all()`` isn't compatible with those. I'll include the from scratch code here as well just in case you need to reference it later (it looks confusing now, but I'm confident child's play if you come back at the end of the course 😉)

```
// this assumes that both the current object, and the other object use sprites with center alignment

// I get the diff to see if we're further apart horizontally, or vertically
x_diff = abs(x - other.x);
y_diff = abs(y - other.y);

// these represent the distance between the 2 centers when a horizontal or vertical collision happens
hor_col_dist = abs((sprite_width + other.sprite_width) / 2);
vert_col_dist = abs((sprite_height + other.sprite_height) / 2);

// I'm applying scaling to keep the logic accurate for wide shapes
// ex. if the ball hits the left side of the paddle, x_diff will be bigger than y_diff, but it's a vertical collision
// So for a horizontal collision, the scaled_x_diff should be 1, and scaled_y_diff should be guarenteed to be less than 1 (hence scaled_x_diff > scaled_y_diff), also vice versa
scaled_x_diff = x_diff / hor_col_dist;
scaled_y_diff = y_diff / vert_col_dist;

// horizontal collision
if(scaled_x_diff > scaled_y_diff){
	if(x < other.x){
		hspeed = -abs(hspeed); // bounce to the left
	}else{
		hspeed = abs(hspeed); // bounce to the right
	}
// vertical collision
}else{
	if(y < other.y){
		vspeed = -abs(vspeed); // bounce up
	}else{
		vspeed = abs(vspeed); // bounce down
	}
}
```

</details>

## How do I select the brick colors?

<details markdown="1">

I don't think anyone is actually asking this question, but I thought I'd share anyway

Selecting a color pallete for your game is a skill on it's own. When I'm in charge of the art (which is thankfully rare), I usually stick to greyscale to make things easy on myself (maybe greyscale w/ a few highlights?)

This time I used coolers.co to help me pick a set of colors to looked good together. [These](https://coolors.co/e0efde-e08d79-a882dd-49416d) are my sepcific colors, feel free to hit space and try some other options for your self

![](../../images/pong/coolers.png)

Once you have a set of coolers from coolers, you can copy the hex codes, and copy them into game maker

![](../../images/pong/hex.png)

</details>

## How do I make the game restart after 3 ball resets?

<details markdown="1">

I added a new variable called ``resets_left`` and started it at 3

Then I decreased it everytime the ball reset

The ball reset also checked if ``resets_left`` was 0, in which case I'd reset

</details>
