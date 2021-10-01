---
layout: default
title: Missile Turret
nav_order: 1
parent: Platformer | Turrets
---

# Missile Turret

We'll be making 2 turrets, the first one will shoot missile, and to make things more interesting we'll make them heat seeking missiles 🎯

## Rotating Turret

Our turret will have 2 parts. The base which will be rotated to attach to the ground (or wall, or ceiling), and the turret, which will be rotate to aim at the player. The base and turret, will be frames 0 and 1 of ``sMissileTurret`` accordingly

Wait a second ... drawing 2 sprites on top of each other with different rotations? This sounds awfully familiar, I don't think you need me for this one

<details data-summary="How to make a rotating turret w/ a still base?" markdown="1">
```
// oTurret Create Event
turret_angle = 0;

// oTurret Step Event
//// GUN ANGLE
{
	turret_angle = point_direction(x, y, oPlayer.x, oPlayer.y);
}

// oTurret Draw Event
// base
draw_sprite_ext(sprite_index, 0, x, y, image_xscale, image_yscale, image_angle, image_blend, image_alpha);
// turret
draw_sprite_ext(sprite_index, 1, x, y, abs(image_xscale), abs(image_yscale), turret_angle, image_blend, image_alpha);
```

``sMissileTurret`` origin: First off, since we have rotation be sure to set the sprite origin at the axis of rotation. May as well set it for the flame turret too while you're at it

![](../../images/platformer/turret_origin.png) <!-- include both missile and flame turret -->

``point_direction``: Previously we've used this to find the direction pointing to the mouse. Now we're using it to find the direction from the turret's position to the player's position

``// base``: We draw the base first since we want it to be behind the turret. With the exception of frame 0, this will use all the default ``draw_sprite_ext`` variables. Also I explictly want to use ``image_angle`` here because I plan to rotate the base in the room editor to make it attach to the walls

``// turret``: Very similar to ``// base``, but we want to use frame 1 instead of frame 0, and we want to use the ``turret_angle`` variable instead of using the default ``image_angle``. Using ``abs()`` on the scale variables is kind of a bonus. If I enlarge the turret in the room editor, I want those size changes to be applied to both the base and the turret. BUT, if use negative scales to reflect the object in the room editor, then that could mess up the look of the turret rotation. Hence ``abs()``

</details>

When we test it out we should see a living rotating turret

![](../../images/platformer/turret_rotation.gif)

## Turret Damage

Now let's have the turret take damage. I think you can actually handle this as well

So we want the turret to lose hp whenever the player's bullets collide with it, and then die when it loses all hp. As a bonus making you can research [``image_blend``](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Asset_Management/Sprites/Sprite_Instance_Variables/image_blend.htm) and use it to adjust the turret color when it's hurt

HINT: I ended up needing [instance_place](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Asset_Management/Instances/instance_place.htm) to get this working, so you can read up on that if you hit issues

<details data-summary="How to make the turret take damage?" markdown="1">

```
// oTurret Create Event
hp = 3;

// oBullet Step Event
//// TURRET COLLISION
{
	if(place_meeting(x+dx, y+dy, oTurret)){
		var turret = instance_place(x+dx, y+dy, oTurret);
		with(turret){
			hp--;
			image_blend = c_red;
			alarm[0] = 10;
		}
		instance_destroy();
	}
}
//// WALL COLLISION
//// APPLY MOVEMENT

// oTurret Alarm 0 event
image_blend = c_white;

// oTurret Step Event
//// GUN ANGLE
//// DEATH
{
	if(hp <= 0){
		instance_destroy();
	}
}
```

``instance_place``: Hopefully the basic structure makes sense. If we're about to collide with a turret, decrease the health within the turret, and then destroy the bullet. ``instance_place`` is the head scratcher, why not just do ``with(oTurret)``? If you do ``with(oTurret)`` and put multiple turrets in the room, the issue will be pretty clear. ``with(oTurret)`` doesn't know which turret you want to hurt, so it just hurts ALL turrets. To fix this we use ``instance_place`` to get the exact turret instance that we're colliding with. ``instance_place`` is almost identical to ``place_meeting`` the only difference is that ``place_meeting`` returns a boolean indicating whether it found another instance, whereas ``instance_place`` returns the actual id of the instance that it found. Now when we use ``with`` we're only executing the code on the turret that we collided with

``image_blend = c_red;``: Here I'm changing blend to red to add a red tint to the sprite.

``alarm[0]``: In this case I only want it to flicker red, and then go back to white (meaning no tint). So I set the alarm to a smaller number (10 is 1/3 of a second), and then when the alarm goes off we switch image_blend back to ``c_white``.

``image_blend = merge_color(c_red, c_white, hp/3);``: An alternative to flickering is to have the turret keep the tint and get more red on every hit. To do that you can replace ``image_blend = c_red;`` with the ``merge_color`` line, and then remove all the alarm logic. ``merge_color`` takes 2 colors and then mixes them depending on the third, amount, parameter. 0 means all red, 1 means all white, and .5 would be and equal mix of both (pink I guess?). So we can calculate amount based on hp to make it become more red as the hp gets smaller

</details>

Once we've added all that our turrets should be killable

![](../../images/platformer/turret_death.gif)

## Basic Missiles

So now we can fight the turrets, but it's kind of an unfair fight. Let's give it some missiles to fight back

I'll let you handle the basic missiles first, then I'll take the wheel and help with the heat seeking. The basic missiles will essentially be the same as the bullets. We want the turret's to make the missiles using a recurring alarm, and then after that the bullets should continue to move in the direction they were shot

<details data-summary="How to code up basic missiles?" markdown="1">

```
// oTurret Create Event
alarm[1] = random_range(3*room_speed, 5*room_speed);
center_to_turret_end_dist = 64;

// oTurret Alarm 1 Event
alarm[1] = random_range(3*room_speed, 5*room_speed);

var turret_end_x = lengthdir_x(center_to_turret_end_dist , turret_angle);
var turret_end_y = lengthdir_y(center_to_turret_end_dist , turret_angle);
with(instance_create_layer(x+turret_end_x, y+turret_end_y, layer, oMissile)){
	image_angle = other.turret_angle;
}
```

Overall this is pretty much identical to the logic we had for the player to fire bullet, w/ a couple of key differences

``alarm[0]``: Instead of trigger on mouse click, I'm triggering the missiles in the alarm, and we use ``alarm[0] = random_range(3*room_speed, 5*room_speed);`` in each event to make sure it keeps firing every 3-5 seconds

``dx`` / ``dy``: In the player shooting version we set the ``dx``/``dy`` for the bullet. In this case I've opted to leave it in angle. We'll get to that in a bit

```
// oMissile Create Event
mv_speed = 3;

// oMissile Step Event
//// MOVEMENT
{
	var dx = lengthdir_x(mv_speed, image_angle);
	var dy = lengthdir_y(mv_speed, image_angle);
}
//// DEATH
{
	if(not place_free(x, y)){
		instance_destroy();
	}
}
//// APPLY MOVEMENT
{
	x += dx;
	y += dy;
}
```

Axis of rotation: Since the missile will be rotating, we need to think of the axis of rotation. Since it's rectangular we could probably put the axis of rotation anywhere along the center of the missile, but I'll just keep thing simple and stick with center-center (but it would be interesting to experimenting with how the axis of rotation affect the look of the missile)

![](../../images/platformer/missile_origin.png)

``var dx`` / ``var dy``: Here I've made ``dx``/``dy`` local variables instead of instance variables. Later we're going to be continuously adjusting the ``image_angle``, instead of having a consistent ``dx`` / ``dy`` as I normally would, I'm going to make ``image_angle`` the primary variable, and recalculate ``dx``/``dy`` on the fly. Aside from that the ``lengthdir`` calculations are identical to the calculations made when the player spawns her bullets

</details>

Now our turret can actually shoot!

![](../../images/platformer/turret_shooting.gif)

## Heat Seeking

It doesn't really matter, since the missiles can't hurt you yet, but you may have noticed that they're pretty easy to dodge. Let's fix that by making them angle toward the player as they fly along

I've been making you do alot of the driving on this section, and you can probably give this one a try as well, but I think I'll take the wheel on this one (I'm getting rusty)

I'll start with a simple approach

```
// oMissile Create Event
rot_speed = 5;
// oMissile Step Event
//// HEAT SEEKING
{
	var target_angle = point_direction(x, y, oPlayer.x, oPlayer.y);
	if(abs(target_angle-image_angle) < rot_speed){
		image_angle = target_angle;
	}else if(target_angle > image_angle){
		image_angle += rot_speed;
	}else{
		image_angle -= rot_speed;
	}
}
//// MOVEMENT
//// DEATH
//// APPLY MOVEMENT
```

``target_angle``: The represents the target or goal for the missile. I couldn't just done ``image_angle = target_angle;``, but this would have made the missile rotate too quickly, and it would be hard to dodge. Instead we have a target, and the missile will work toward that target over multiple steps

``abs(target_angle-image_angle)``: The if statement basically says "if the ``target_angle`` is bigger, make the ``image_angle`` bigger, otherwise make the ``image_angle`` smaller". But we can't leave it at that, otherwise if it's too close it'll spasm back and forth every frame (we had the same issue back in "Pong | Extreme Edition | Upkeep" fixing AI spasms). So instead we first use ``abs()`` to check the distance to the ``target_angle`` and lock to the ``target_angle`` if we're too close

When you test this out it works pretty good 😊

![](../../images/platformer/turret_heatseek_good.gif)

... or at least for the most part 😩

![](../../images/platformer/turret_heatseek_edgecase.gif)

## Heat Seeking Edge Case

An example of where we'd hit this issue is if the ``target_angle`` is 10, and the ``image_angle`` is 350

![](../../images/platformer/turret_heatseek_edgecase_example.png)

To the eye to clear shortest path is for ``image_angle`` to increase, but the code see that ``target_angle < image_angle`` and so it decreases. It's doesn't take into account that the angle wraps around and that 360 and 0 are the same place. Luckily game maker has a function called ``angle_difference()`` to the rescue. If we test out ``angle_difference(10, 350)`` it's smart enough to figure out the actual shortest distance and it returns ``20``

```
// oMissile Step Event
//// HEAT SEEKING
{
	var target_angle = point_direction(x, y, oPlayer.x, oPlayer.y);
	var diff = angle_difference(target_angle, image_angle);
	if(abs(diff) < rot_speed){
		image_angle = target_angle;
	}else if(diff > 0){
		image_angle += rot_speed;
	}else{
		image_angle -= rot_speed;
	}
}
//// MOVEMENT
//// DEATH
//// APPLY MOVEMENT
```

``abs(diff)``: Hopefully this feels like a naturaly extension of the previous implementation. We used to apply ``abs()`` to ``target_angle-image_angle``, which is the error prone way to find the difference, so we just replace that with the ``angle_difference()`` output to fix things

``diff > 0``: This is also a natural extension of the previous code, but it's a little less obvious. The original version was ``target_angle > image_angle``, but we could write that as ``target_angle-image_angle > 0`` and that'll still be valid because math (if you subtract the same value from both sides, in this case ``image_angle``, then comparison will still be the same). Now we can see the error prone difference again ``target_angle-image_angle``, so we just replace that with the new ``diff`` and we're done

> **Can you do this in less than 9 lines of code?** It's kind of bulky isn't it 🤓. I was trying to think through shorter way, but then I realized that the angle_difference documentation example already has a slick 3 line solution [docs](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Maths_And_Numbers/Angles_And_Distance/angle_difference.htm). Just be aware that they opted to reverse the order of the angle_difference parameters. I think my ordering is more intuitive, but to each their own I guess

Now we have true edge-caseless heat seeking missiles 🎉🍾

![](../../images/platformer/turret_heatseek_edgecase_fixed.gif)
