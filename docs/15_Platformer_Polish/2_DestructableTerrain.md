---
layout: default
title: Destructable Terrain
nav_order: 2
parent: Platformer | Breakables
---

# Destructable Terrain

Next we'll add destructable terrain. This is a fairly simple trick, but it's definitely worth applying, as it's yet another detail that makes your world feel more real

## Burnable Grass

Since we have missiles and flames in our game, I figured we'd do breakable objects a little differently and make them burnable instead. I think you can do it. Use ``sGrass`` as the sprite, and then make it toggle to frame 1 when it get's hit by missiles or flames

<details data-summary="How to add burnable grass?" markdown="1">


```
// oGrass Create
image_speed = 0;

// oMissile Step Event
//// MOVEMENT
//// DEATH
//// HURT PLAYER
//// HURT GRASS
{
	if(place_meeting(x, y, oGrass)){
		with(instance_place(x, y, oGrass)){
			image_index = 1;
		}
	}
}

// oSpark Step Event
//// MOVEMENT
//// DEATH
//// HURT PLAYER
//// HURT GRASS
{
	if(place_meeting(x, y, oGrass)){
		with(instance_place(x, y, oGrass)){
			image_index = 1;
		}
	}
}
```

This should be pretty straight forward, the projectile changes the grass's frame to 1 when collisions happen

``instance_place``: If you need a refresher on this, be sure to refer back to "Platformer | Turrets | Missile Turrets

**parenting**: This current scenario also highlights the case for parenting. ``oMissile`` and ``oSpark`` have the same behaviour, so it could be worth moving some of that shared code to a ``oEnemyBullet`` object

</details>

Now when we test it out we should have burnable grass! Now the world feels a little more dynamic, and the turrets feel a little more mean 😿

![](../../images/platformer/burnable_grass.gif)

## Curable Terrain

But what about the player bullets? Well, I didn't want the player to be just another source of destruction, to let's give the player's bullets a green thumb. Can you make the player bullets heal (unburn), they collide?

<details data-summary="Can you make the player bullets cure the grass?" markdown="1">
```
// oBullet Step
//// TURRET COLLISION
//// WALL COLLISION
//// APPLY MOVEMENT
//// GRASS COLLISION
{
	if(place_meeting(x, y, oGrass)){
		with(instance_place(x, y, oGrass)){
			image_index = 0;
		}
	}
}
```

Same code as before 😉
</details>

Now the grass can be healed as well as burned 😍 (I guess we have aloe bullets?)

![](../../images/platformer/curable_grass.gif)

## More Burnables

No need to settle with grass. Let's make more burnable things! This is actually a great opportunity for parenting and polymorphism. Let's add a bush and then generalize the 2 burnables into a ``oBurnable`` object

<details data-summary="How to apply parenting to the burnables?" markdown="1">
```
// oBurnable Create
image_speed = 0;

// oGrass Create
//// delete code and let it inherit from oBurnable

// oBush Create
//// this should also inherit from oBurnable

// oBullet Step
//// TURRET COLLISION
//// WALL COLLISION
//// APPLY MOVEMENT
//// BURN BURNABLES
{
	if(place_meeting(x, y, oBurnable)){
		with(instance_place(x, y, oBurnable)){
			image_index = 0;
		}
	}
}

// oMissile Step Event
//// MOVEMENT
//// DEATH
//// HURT PLAYER
//// BURN BURNABLES
{
	if(place_meeting(x, y, oBurnable)){
		with(instance_place(x, y, oBurnable)){
			image_index = 1;
		}
	}
}

// oSpark Step Event
//// MOVEMENT
//// DEATH
//// HURT PLAYER
//// BURN BURNABLES
{
	if(place_meeting(x, y, oBurnable)){
		with(instance_place(x, y, oBurnable)){
			image_index = 1;
		}
	}
}
```

Here I've created a generic ``oBurnable`` object, assigned it to be the parent of both ``oGrass`` and ``oBush``

We get some code sharing benefit since we don't need to write out ``image_speed`` twice, but the bigger benefit is the polymorphism. Now ``oPlayer``, ``oMissile``, and ``oSpark`` only need to handle collisions with ``oBurnable`` then it get's both ``oGrass`` and ``oBush`` collisions for free 😎

> **I still see a lot of duplicate code, the burnable collision, looks very similar the player collision**: Correct 🤓! In the polymorphism section I mentioned that we could, in theory, lump ``oPlayer``, and ``oTurret`` under a super generic ``oTargetable`` parent. We could try lumping ``oBurnable`` into that mold as well. It would just have a max hp of 1. If we did that the bullet's would only need to handle collisions w/ ``oTargetable`` 😲. Proceed w/ caution though, each targetable has slightly different behaviours when the collisions happen, so you'll need to be careful accounting for that in the generic parent

</details>

Burnable bushes and grass ✅

![](../../images/platformer/burnable_bushes.gif)

## Further Inspiration

Destructable terrain comes up in a lot of games, but I was specifically inspired by this [vlaambeer talk](https://www.youtube.com/watch?v=AJdEqssNZ-U). He goes over a bunch of easy tricks you can apply to make your game more polished, so it's definitely worth checking out

Also, if we look at the way hollow knight does destructable terrain, you can see that a bunch of pieces fly out

![](../../images/platformer/hollow_knight_destructable_terrain.gif)

Feel free to try adding that as a side exercize, I've got a video [here](https://www.youtube.com/watch?v=OVR17nInEwc) on exploding coins that can be used as a starting point
