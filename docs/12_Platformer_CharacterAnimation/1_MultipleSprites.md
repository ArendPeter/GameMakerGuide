---
layout: default
title: Multiple Sprites
nav_order: 1
parent: Platformer | Character Animation
---

# Multiple Sprites

You probably noticed that we have 3 player sprites ``sPlayerJump``, ``sPlayerIdle``, and ``sPlayerWalk``. Let's use them 😄

## Animation States

I'll call these 3 sprites "animation states". We need to code up logic to handle each state and also know how to switch between them. Here a little descriptor of how we want each state to animate

| State | When to animate | How to animate |
|--|--|--|
| Jump | When player is off the ground | Use frame 0 on the way up, and 1 on the way down |
| Idle | When player is on the ground, and isn't moving | freeze on frame 0 |
| Walking | When player is on the ground, and is moving | loop |

> **Why are you calling them states?**: oh because state machines 🤓! State machines come up all the time in game development (animation, AI, UI, etc). You make a state machine by formally breaking down a behaviour into states, and then defining the functionality of each state, as well as how to transition between them. That's essentially what we're doing here so I'm using the terminology, but I felt like a full state machine overview would be out of scope for this course so I didn't want to go any further than that

So with that information can you write up some if statements to check for those states and set the ``sprite_index``, ``image_index``, and ``image_speed`` accordingly?

<details data-summary="Try coding up the animation states" markdown="1">

```
// oPlayer Step Event
//// HORIZONTAL
//// VERTICAL
//// ANIMATION
{
	// jumping
	if(place_free(x,y+1)){
		sprite_index = sPlayerJump;
		image_speed = 0;
		if(dy < 0){
			image_index = 0;
		}else{
			image_index = 1;
		}
	// idle
	}else if(dx == 0){
		sprite_index = sPlayerIdle;
		image_speed = 0;
		image_index = 0;
	// walking
	}else{
		sprite_index = sPlayerWalk;
		image_speed = 1;
	}
}
```

**Jumping**: We check that we're on the ground using ``place_free(x,y+1)`` (the same check we did for jumping). Then we set the sprite accordingly. Since we're directly setting the frames, we want ``image_speed = 0;`` (otherwise game maker would automatically update the frames). Then we do ``image_index = 0;`` when going up, and ``image_index = 1;`` otherwise

**Idle**: Since this is the else, we already know we're on the ground, but we need to add an extra check to verify that we're not moving. After setting the sprite accordingly, we want to freeze on the first frame. So that means no animation, ``image_speed = 0;``, and to lock to the first frame ``image_index = 0;``. Remember the frame count starts at 0, not 1

**Walking**: Since this is another else we know neither of the previous 2 conditions passed. So that matches our condition perfectly, (i.e. *we're on the ground, and we're moving*). We just want this to animate without locking to a particular frame, so after setting the sprite we set ``image_speed = 1;``, and don't bother with ``image_index`` (we want the animation speed to handle that)

</details>

When you test it out, you'll notice that it looks really wonky, any ideas why?

![](../../images/platformer/animation_no_alignment.gif)

## Alignment Issues

You guessed it, it's because of alignment issues, can you fix them?

<details data-summary="Can you fix the alignment issues?" markdown="1">

We need to fix up the sprite offsets for ``sPlayerWalk`` and ``sPlayerJump``. For example, when we have ``sPlayerIdle`` our offset is to the bottom center, and when we switch to ``sPlayerWalk``, the x/y will stay the same, but x/y now represents the top left of the sprite. As a result ``sPlayerWalk`` will be displayed below and to the right of where ``sPlayerIdle`` was

![](../../images/platformer/idle_lined_up_with_walk.gif)

So to make their animations line up better we'll update all of them to use the bottom center

![](../../images/platformer/updated_walk_jump_offsets.png)

Bottom center tends to be a pretty good alignment point in general. For this particular case centering it on the body would probably work, but if you imagine a crouching scenario the feet is the only point that really stays fixed

</details>

Yay! Our player looks much more alive now!

![](../../images/platformer/animation_with_alignment.gif)

But if you play it long enough, you might still hit some issues. Can you find them?

## Collision Masks

![](../../images/platformer/animation_bad_collision_mask.gif)

That's right, now we have an issue where we can get stuck in walls. Why do you think this happens? Can you fix it?

<details data-summary="How do we avoid getting stuck in walls?" markdown="1">

This is a collision mask issue (I told you we'd have one before the end of the course 😉). Since our sprites have different collision mask sizes, when we switch between them the new collision mask could overlap with a solid whereas the previous one didn't, thus leaving us stuck in the wall

To fix this we need to make sure the collision masks on each of our sprites match. That way we can avoid collision mask surprises when switching between sprites

![](../../images/platformer/updated_collision_masks.png)

</details>

Now this issue is gone 😍

![](../../images/platformer/animation_good_collision_mask.gif)

## Terinary Operator

We haven't learned any new Game Maker syntax in a while, and I was itching to use ternary operators in our previous code segment, so let's do some learning 😜

A ternary operator is like an if statement compressed on one line, here's an if statement from our animation code, as well as the updated ternary version

```
// standard if statement
if(dy < 0){
	image_index = 0;
}else{
	image_index = 1;
}

// ternary operator
image_index = (dy < 0)? 0 : 1;
```

And here's a more formal break down

```
(condition)? value_when_condition_is_true : value_when_condition_is_false;
```

So if you have simple if else statements like that which only set a variable, that's a great opportunity to use the ternary operator

Want some more practice? I gotchu, here's some other code

```
if(not place_free(x, y+dy)){
    if(dy > 0){
        move_contact_solid(270, abs(dy));
    }
    if(dy < 0){
        move_contact_solid(90, abs(dy));
    }
    dy = 0;
}
```

<details data-summary="How would you update the above code using ternary operator?" markdown="1">

```
if(not place_free(x, y+dy) and dy != 0){
    move_contact_solid((dy > 0)? 270 : 90, abs(dy));
    dy = 0;
}
```

``(dy > 0)? 270 : 90``: This is the key part. Previously we had to write out the ``move_contact_solid`` twice just to change that one value. Now we can embed that inside a ``move_contact_solid`` statement, all embeded in one line

``and dy != 0``: This part is a little less obvious. Changing to a ternary operator did slightly modify our logic. Previously the ``dy == 0`` wouldn't trigger either of the if statements, and ``move_contact_solid`` would be skipped. With the ternary operator, ``dy == 0`` would fall into the else clause and use 90 degrees. This is extra bad because the [move_contact_solid() documentation](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Movement_And_Collisions/Movement/move_contact_solid.htm) shows that 0 for the second parameter is interpretted as infinite distance. That would leave us open to an edge case where the player flies off into the sky indefinitely 😁 (but that would be kind of funny, hopefully some of you already hit that bug before realizing what was happening). To avoid this scenario I added the ``and dy != 0`` check to ensure that ``dy == 0`` case is still skipped (as a reminder ``!=`` means not equal)

</details>

> **So should I always use ternary operator whenever possible?** hmm 🤓, well you'll need to use your best judgement there. They're much more compact, but they can also be harder to read. So, for me, I avoid them if I think the readability cost is too great. I used to go crazy with them, sometimes even using ternary operators inside ternary opertators but then at work they told me to stop doing that, and in my older age I've come see the sense in that

Here's an example of how not to do ternary operators

```
sprite_index = (place_free(x,y+1))? sPlayerJump : ((dx == 0)? sPlayerIdle : sPlayerWalk);
```
