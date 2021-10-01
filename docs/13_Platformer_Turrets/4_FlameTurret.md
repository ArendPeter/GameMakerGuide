---
layout: default
title: Flame Turret
nav_order: 4
parent: Platformer | Turrets
---

# Flame Turret

Now let's make a turret that shoots flames 🔥🔥🔥

## Code Reuse

So to start off with make a turret with a base, and a turret and have the turret rotate toward the pl- ...

Wait, didn't we already do that in ``oTurret``? Let's find a way to reuse that

Let's list out what oTurret currently does

1. Rotates toward the player w/ a static base
2. Takes damage from player bullets
3. Shoots occasional missiles

And here's what I want ``oFlameTurret`` to do

1. Rotates toward the player w/ a static base
2. Takes damage from player bullets
3. Rapidly shoot sparks
4. Stop shooting if the player is too far away

So 1 and 2 are the same, but 3 and 4 are different, hmm

We hit a similar problem when working on flappy bird. We had buttons that went to different rooms, but we handled it by making a single button that could take the room as one of it's variables

This is different, in the button case we got the results we wanted by specifying different data for each instance, but this time we need to specify completely different behaviour (in particular part 4 of the ``oFlameTurret`` requirements)

Luckily Game Maker has a tool for this, and it's called parenting 🐣

## Parenting

Make 2 fresh objects, ``oMissileTurret``, and ``oFlameTurret``. Then set both of their parents to our previous ``oTurret`` object

![](../../images/platformer/setting_turret_parent.gif)

Now if you look at ``oMissileTurret``, and ``oFlameTurret`` they have a bunch of grayed out events, and clicking into the events shows that they extactly match ``oTurret``. ``oMissileTurret``, and ``oFlameTurret`` are both essentially clones of ``oTurret`` right now (they're inheriting everying from their parent)

To verify this, try going to the room and swap all the ``oTurret`` instances w/ ``oMissileTurret``. When we run the game, the change shouldn't be noticable at all

Now ``oTurret`` has pieces that are common to both turrets, as well as pieces that are specific to ``oMissileTurret``. We don't want ``oFlameTurret`` to inherit anything that it doesn't need, so let's try moving all that missile specific stuff from ``oTurret`` to ``oMissileTurret``

To start, copy the from the ``oTurret`` alarm 1 event then delete the alarm 1 event. Here's the code for reference

```
alarm[1] = random_range(3*room_speed, 5*room_speed);

var turret_end_x = lengthdir_x(center_to_turret_end_dist , turret_angle);
var turret_end_y = lengthdir_y(center_to_turret_end_dist , turret_angle);
with(instance_create_layer(x+turret_end_x, y+turret_end_y, layer, oMissile)){
	image_angle = other.turret_angle;
}
```

Now when you go to ``oMissileTurret`` it should no longer inherit the alarm 1 event (there's no longer an alarm 1 event to inherit). So let's make a new alarm event and paste the code

Next let's do the create event. This is a little trickier since we want part of it to stay in the parent (specifically ``hp = 3;``), but we want the rest of it to be in child (the alarm and distance variables). This time only cut out the piece you need from ``oTurret``. Here's the specific snippet

```
alarm[1] = random_range(3*room_speed, 5*room_speed);
center_to_turret_end_dist = 64;
```

Now when we go ``oMissileTurret``, right click on the greyed out create event, and select **Inherit Event**. You'll actually notice that there were more options there, let's go over those

* **Inherit Event**: Here's we still inherit the code from the parent event, but then we execute some of our own code on top of it
* **Override Event**: Here we override all the code from the parent. We still inherit other events from the parent, but for this particular event, we only execute our code. Not the parent's

In this case we're doing in **Inherit Event** since we still want that ``hp = 3;`` line, we just want to add some more lines of our own on top of it

When we inherit, you notice that it automatically adds a ``event_inherited();`` function. This function just executes all the code that would have occurred in the parent. If we decide we want to override later we can just delete that line

Now we've cleaned up ``oTurret``, it still has the rotation and damage systems, but now all the shooting has been moved over to ``oMissileTurret``. Now that we have a starting point for ``oFlameTurret`` we can add the specifics

## Sparks

Sparks are pretty much identical to missile, except for the sprite and that we want it to do less damage. This would be another good opportunity for inheritence (and that would be a great exercise if you want to give it a try!), but I'll just do it the old fashioned way. Let's sync back up when we're done 😉

<details data-summary="How to implement the sparks?" markdown="1">

```
// oSpark Create
move_speed = 10;
dx = 0;
dy = 0;
angle = 0;

// oSpark Step
//// MOVEMENT
{
	var dx = lengthdir_x(move_speed, angle);
	var dy = lengthdir_y(move_speed, angle);
	x += dx;
	y += dy;
}
//// DEATH
{
	if(not place_free(x, y)){
		instance_destroy();
	}
}

//// HURT PLAYER
{
	if(place_meeting(x, y, oPlayer)){
		with(oPlayer){
			hp -= 2;
		}
		instance_destroy();
	}
}
```

Hopefully no surprises there
</details>

## Shooting Sparks

Now that we have sparks, let's have the flame turret shoot them 😈

The implementation for shooting sparks will be similar to the missile turret shooting missiles. Here's a break down of the pieces

1. We have an alarm event to trigger the shots (this will be triggered much more often than the missile rate)
2. The alarm logic should only be triggered if the player is close enough
3. We create a spark and have it move roughly toward the player (I also added some randomness to make it more interesting)

Go ahead and give it a try, I think the only missing piece you'll need is to review the docs for [point_distance()](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Maths_And_Numbers/Angles_And_Distance/point_distance.htm)

<details data-summary="How does the flame turret shoot sparks?" markdown="1">

```
//// FLAME TURRET CREATE
event_inherited();

alarm[0] = 2;

//// FLAME TURRET ALARM[0]

var dist_to_player = point_distance(x, y, oPlayer.x, oPlayer.y);

if(dist_to_player < 700){
	with(instance_create_layer(x, y, layer, oFlame)){
		angle = other.gun_angle+random_range(-10, 10);
	}
}

alarm[0] = 2;
```

``event_inherited();``: As a reminder this will execute all the code from the corresponding parent event. In this case that just means executing ``hp=3;`` from the ``oTurret`` create event

``alarm[0] = 2;``: I opted for a much higher fire rate on this one, it should look like a machine gun spray of sparks

``var dist_to_player = point_distance(x, y, oPlayer.x, oPlayer.y);``: This is a new function that follows a similar format to ``point_direction``. You give it the x/y's for 2 points, and then it'll give you the distance between those 2 points

> **How does point_distance fit into the polar vs cartesian topic?** Quite nicely 🤓 ! So I mentioned how you can use ``lengthdir_x`` and ``lengthdir_y`` to convert from polar to cartesian. They take a distance and an angle, and spit out horizontal and veritcal components. ``point_direction`` and ``point_distance`` are essentially the same for converting from cartesian to polar!! You can give 2 points and then they'll spit out the angle and distance. If you want to do a horizontal/vertical component instead of 2 points, you can just use (0,0) for the first point and (horizontal, vertical) for the second. We've come full circle 🥳

``dist_to_player < 700``: We can use the resulting distance to disable the spark creation whenever the player is more than 700 pixels away

``angle = other.gun_angle+random_range(-10, 10);``: Adding the random range here gives a spray effect, like it's shooting the bullets in a cone. This helps make it look more organic, otherwise you would be a straight chain of sparks

</details>

And when we test it out, it should look something like this 😁

![](../../images/platformer/flame_turret_with_sparks.gif)
