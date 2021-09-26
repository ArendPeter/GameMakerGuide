---
layout: default
title: Bullets
nav_order: 3
parent: Platformer | Character Animation
---

# Bullets

Now that we can aim, let's see if we can fire

## Bullet Movement

First we need to create an ``oBullet`` object. For this we just want variables for it's ``dx`` and ``dy`` then it should move according to those variables. Then we want the bullet to be destroyed if it hits something solid. Want to give it a try?

<details data-summary="How to make bullets move in a single direction using dx/dy?" markdown="1">

```
// oBullet Create Event
image_speed = 0;
move_speed = 10;
dx = 0;
dy = 0;

// oBullet Step Event
//// WALL COLLISION
{
	if(not place_free(x+dx, y+dy)){
		instance_destroy();
	}
}

//// APPLY MOVEMENT
{
	x += dx;
	y += dy;
}

```

Yep, that's it, no rocket science

``image_speed = 0;``: If you tested the game you probably noticed the bullet was animated. I'm starting the animation speed at 0 to stop this

``move_speed = 10;``: This is a variable we're not ready to use yet, but it'll come in handy later when we're ready to set dx and dy

</details>

## Shooting

Now let's actually make the bullets spawn from the gun. This is a little trickier

```
// oPlayer Create
shoulder_to_gun_end_dist = 90;

// oPlayer Step
//// SHOOTING
{
	if(mouse_check_button_pressed(mb_right)){
		gun_end_x = lengthdir_x(shoulder_to_gun_end_dist , arm_angle);
		gun_end_y = lengthdir_y(shoulder_to_gun_end_dist , arm_angle);
		bullet = instance_create_layer(
			x+arm_x+gun_end_x,
			y+arm_y+gun_end_y,
			layer,
			oBullet
		);
		with(bullet){
			dx = lengthdir_x(move_speed, other.gun_angle);
			dy = lengthdir_y(move_speed, other.gun_angle);
			image_angle = other.gun_angle
		}
	}
}
```


cartesian vs polar: We're back to the cartesian vs polar discussion. Here want want the bullet to have the direction match the ``arm_angle``, but since the bullet uses ``dx`` / ``dy`` we need to convert from the polar way of thinking to the carteisian. That's where ``lengthdir_x`` and ``lengthdir_y`` come in handy

``lengthdir_x()`` / ``lengthdir_y()``: This functions take a distance and an angle, and then gives back the cartesian horizontal and vertical components respectively. Below is an example where you're moving 5 pixels away at an angle 38.65 degrees. This is the equivalent of moving 4 to the right, and 3 up, so ``lengthdir_x(5, 38.65)`` and ``lengthdir_y(5, 38.65)`` would give us 4 and 3 respectively

![](../../images/platformer/lengthdir.png)

``shoulder_to_gun_end_dist``: The distance from the point where the arm attaches to the shoulder to the tip of the gun where the bullets spawn. I just referenced the ``sArm`` sprite, so you can adjust it accordingly

``gun_end_x`` / ``gun_end_y``: These represent the horizontal and vertical distances from shoulder to the tip of the gun. ``should_to_gun_end_dist`` and ``arm_angle`` are the polar equivalent, so we need to use the ``lengthdir`` functions to convert between them

``x+arm_x+gun_end_x`` / ``y+arm_y+gun_end_y``: We've got lots of relative distances at this point. So we need to add them together to find the room position for the end of the gun

![](../../images/platformer/bullet_y_parts.png) <!-- Draw lines to visualize the y+arm_y+gun_end_y calculations -->

TODO: add axis of rotation piece, and mention that image_angle is sufficient in this case

![](../../images/platformer/bullet_origin.png)

``with``/ ``other``: ``with`` let's us execute code in another instance (in this case the bullet), and then we can use ``other`` within that code to refer back to the instance that called ``with`` (which in this case is equivalent to ``oPlayer``. We've used ``with`` and ``other`` before, but it's been a while, feel free to check back at "Pong | Extreme Edition | Power Ups" for more details

``dx`` / ``dy``: Another polar conversion. The bullet should move in the same direction that the gun is pointing, and it should use the ``move_speed`` variable we defined in the bullet create event for the speed. But we need the ``lengthdir`` functions to convert these to numbers we can use for ``dx`` and ``dy``

That was quite a lenghtly addition, but when we test it out we should finally be able to shoot 😁

![](../../images/platformer/shooting.gif) <!-- Draw lines to visualize the y+arm_y+gun_end_y calculations -->

## Variable Scope

Our gun doesn't have a scope, but that doesn't mean our variables can't have them! 🥁🥁📀

```
// oPlayer Step
//// SHOOTING
{
	if(mouse_check_button_pressed(mb_right)){
		var gun_end_x = lengthdir_x(shoulder_to_gun_end_dist , arm_angle);
		var gun_end_y = lengthdir_y(shoulder_to_gun_end_dist , arm_angle);
		var bullet = instance_create_layer(
			x+arm_x+gun_end_x,
			y+arm_y+gun_end_y,
			layer,
			oBullet
		);
		with(bullet){
			dx = lengthdir_x(move_speed, other.gun_angle);
			dy = lengthdir_y(move_speed, other.gun_angle);
			image_angle = other.gun_angle
		}
	}
}
```

``var``: I added ``var`` to several of the variables to make them local. Local means the variables will cease to exist once you leave the event, and we should try marking variables accordingly whenever they're short lived

Way back in "Pong \| Your First Game \| Paddle Names", I mentioned that all variables have instance scope by default, but now you're ready to hear the rest of the them!

|Scope Type|Definition|Syntax|
|---|---|---|
| Local | Only accessible from current event | ``var name = 3;`` |
| Instance | Only accessible from current instance (except via the dot operator) | ``name = 3;`` |
| Global | Accessible from anywhere in the game | ``global.name = 3;`` |

**When to use local variables**: Whenever you can! Local scope won't always be viable, but if a variable is only used within an event then make it local! There are a couple benefits I'll get into

**Local variables avoid bugs**: Each instance variable you create, comes with a promise to keep it up to date for the lifespan of the instance. So if we keep ``gun_end_x`` as an instance variable, and we tried to use it somewhere else (maybe the draw event), it would most likely be out of date because it only gets updated when the mouse button is pressed. Making it local ensures that we only want to use it for this specific case, and that avoids bugs that could come up if we tried to use it in the future

**Local variables are more readable**: In addition to avoiding bugs, local variables also make your code more readable. Imagine you're reading old code, and you see an instance variable. To understand how it's used you'll need to scan through all code in the object (and possibly further if you consider dot operators). With local variables I don't have to scan through nearly as much code, and I can make updates without worrying about breaking some other far corner of the game. Programmers are known for writing code, but in reality most of their time is spent reading code, so this is also a big win.

> **Does variable scope affect performance?** Yes it does actually, or at least in theory 🤓! Computers run faster when they manage less data ([because caching](https://www.youtube.com/watch?v=6FyXURRVmR0)). With local variables, your game can just run the event, delete the local variables, and then use that space for other needs, so using ``var`` should make your game use less memory and run faster. That said, when I tested it I saw a 5.5% decrease in memory usage, but the frame rate was about the same [more details](../../images/platformer/var_perf_test.png). Still a win overall, but I thought I would have seen a framerate improvement as well

**When to use global variables**: If you have something that's accessed in a lot of different areas, then you can consider making it global. For example, there are probably a lot of objects that can impact your point total, so that could be a global

**When to use global**: Never? Personally I don't like to use global. I gave a lot of reasons for why local variables are preferred over instance variables (when possible), and those same reasons apply for instance vs global variables. As a general rule, smaller scopes are always cleaner. When I do have a variable that needs to be accessed by a lot of objects, I assign the variable to some general control object (maybe ``oControl.points``). Then I can at least have a instance that is responsible for the variable (so if I wanted to add logic to keep the points from going negative, ``oControl`` would be the logical place)

> **What about globalvar?** You may see ``globalvar`` referenced at some point. This is similar to ``global.`` but it's usage has been deprecated [see docs](https://docs2.yoyogames.com/source/_build/3_scripting/3_gml_overview/6_scope.html). So if you want to use global variables be sure to use ``global.`` instead
