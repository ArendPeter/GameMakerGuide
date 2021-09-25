---
layout: default
title: Bullets
nav_order: 3
parent: Platformer | Character Animation
---

# Bullets

Now that we can aim, let's see if we can fire

## Bullet Movement

First we need to create an ``oBullet`` object. For this we just want variables for it's ``dx`` and ``dy`` then it should move according to those variables. Want to give it a try?

<details data-summary="How to make bullets move in a single direction using dx/dy?" markdown="1">

```
// oBullet Create Event
image_speed = 0;
move_speed = 10;
dx = 0;
dy = 0;

//// oBullet Step Event
x += dx;
y += dy;
```

Yep, that's it, no rocket science

``image_speed = 0;``: If you tested the game you probably noticed the bullet was animated. I'm starting the animation speed at 0 to stop this

``move_speed = 10;``: This is a variable we're not ready to use yet, but it'll come in handy later when we're ready to set dx and dy

</details>

## Shooting

Now let's actually make the bullets spawn from the gun. This is a little trickier

```
// oPlayer Create
shoulder_to_gun_tip = 90;

// oPlayer Step
//// SHOOTING
{
	if(mouse_check_button_pressed(mb_right)){
		gun_end_x = lengthdir_x(shoulder_to_gun_tip , arm_angle);
		gun_end_y = lengthdir_y(shoulder_to_gun_tip , arm_angle);
		bullet = instance_create_layer(
			x+gun_x+gun_end_x,
			y+gun_y+gun_end_y,
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

## Variable Scope

```
// oPlayer Step
//// SHOOTING
{
	if(mouse_check_button_pressed(mb_right)){
		var gun_end_x = lengthdir_x(shoulder_to_gun_tip , arm_angle);
		var gun_end_y = lengthdir_y(shoulder_to_gun_tip , arm_angle);
		var bullet = instance_create_layer(
			x+gun_x+gun_end_x,
			y+gun_y+gun_end_y,
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


``var``: A new keyword 🎉! When you're creating new variables, you can put ``var`` in front to indicate that they're a local variable

Game Maker has 3 variable scopes local, instance, and global. Way back in Pong | Your First Game | Paddle Names, I mentioned that all variables have instance scope by default, this means that they only exist within the object, and can't be accessed outside the object without using the dot operator
