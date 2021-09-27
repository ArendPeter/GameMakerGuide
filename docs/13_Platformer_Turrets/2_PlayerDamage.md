---
layout: default
title: Player Damage
nav_order: 2
parent: Platformer | Turrets
---

# Player Damage

Even after all that work making the turret, it's still not a fair fight. The player is invisible 💪🏼, let's fix that

## Health

Let's add an ``hp`` variable to the player that's similar to the turret. It just needs to go down when the player hits the missile

<details data-summary="How to add player health" markdown="1">

```
// oPlayer Create Event
hp = 100;

// oMissile Step Event
//// HURT PLAYER
{
	if(place_meeting(x, y, oPlayer)){
		oPlayer.hp -= 10;
	}
	print(oPlayer.hp);
}
```

Since we don't have any feedback yet, I've added a ``print()`` temporarilty so we can verify that it works

</details>

Now we can see the player health go down in the console

![](../../images/platformer/player_hp_console.png)

## Change Player Color

We want to add a similar red flash effect that we used for the turrets, but unfortunately ``image_blend`` doesn't really work very well when the image is already colorful. I'll show you a different way to do it

```
// oPlayer Create Event
is_hurt = true;

// oPlayer Draw Event
//// SET COLOR
gpu_set_fog(is_hurt, c_red, 0, 1);
//// ARMS
//// RESET COLOR
gpu_set_fog(false, c_red, 0, 1);

// oMissile Step Event
//// HURT PLAYER
{
	if(place_meeting(x, y, oPlayer)){
		with(oPlayer){
			hp -= 10;
			is_hurt = true;
			alarm[0] = 10;
		}
		instance_destroy();
	}
}

// oPlayer Alarm 0 Event
is_hurt = false;
```

``with(oPlayer)``: I opted for with instead of dot operators for this one. The dot operator worked great when it was only 1 variable, but it's a bit tedious for 3

``is_hurt``: We'll be toggling the color on and off, so we need a variable to keep track of that. Similar to the turret, we'll have it toggled on when the player is hurt, and then it will toggle back off using the alarm

**flag**: ``is_hurt`` and  is an example of a flag. flags are boolean variables (they're essentially synonyms) that you toggle on temporarily to handle specific scenarios. You'll find your self using these a lot whenever you want to add new behaviours to your objects

``gpu_set_fog(..)``: This is similar to ``draw_set_color()`` or ``image_blend``, where it tells the future functions which color to use. However ``gpu_set_fog`` replaces the existing colors, where as ``image_blend`` only tints. So it's handy when you're trying to recolor images. The first 2 parameters are the only ones that matter. The first is whether the effect is enabled, and the second is the color we want to use

> **But why is it called fog?**: The way we use this function is kind of a hack, it was originally meant for fog effects in 3D games (yes, Game Maker has 3D support 😲). It would essentially recolor objects depending on how far off in the distance they were (like fog!). With 2D that doesn't really make sense, everything is the same distance from the camera. So we just always use 0 and 1 for the 2 parameters, and that makes the color completely opaque

Sweet, this feedback makes the death a lot more clear

![](../../images/platformer/player_damage_flash.gif)

## Player Death

I want to add death next. We could just have the game restart when the hp hits 0, but let's add an animation to make it less abrupt. First let's add a flag so that we can easily check if we're dying

```
//oPlayer Create Event
is_dying = false;

// oPlayer Step Event
//// DEATH
{
	if(hp < 0){
		is_dying = true;
	}
}
```

This ``is_dying`` will be used to update several areas of code. Which areas do you think we'll need to fix?

<details data-summary="Which areas will we need to update?" markdown="1">

There are 3 areas I can think of
 * Animation (Step Event): We need to add support for the death animation
 * Draw Event: If you look at the dying animation, she actually has arms 😲, so we'll need to update the draw event so she doesn't have 4 arms
 * Disable Control (Step Event): It would look kind of weird if the player was still moving and shooting while dying, so let's disable that

</details>

Let's start with the animation. I think you can handle this one. Try adding another state to our animation logic

<details data-summary="How to update the animation logic for death?" markdown="1">
```
//// ANIMATION
{
	// death
	if(is_dying){
		if(sprite_index != sPlayerDeath){
			image_index = 0;
		}
		sprite_index = sPlayerDeath;
		image_speed = 1;
		if(image_index + image_speed >= image_number){
			game_restart();
		}
	// jumping
	}else if(...){

	}...
}
```

``if(is_dying)``: Whenever the ``is_dying`` flag is enabled, we want to have the dying animation, so I just added it as the first condition in the if chain so it can be a catch all

``sprite_index != sPlayerDeath``: When we start dying, we want to make sure the animation starts at frame 0. Using ``sprite_index != sPlayerDeath`` is my hacky way of checking whether we're at the start of the animation (i.e. if we haven't changed sprites yet, then we know we're on the first frame of the animation)

**Single Responsibility Principle**: I could have just added ``image_index = 0;`` to the step event where ``is_dying`` is set to ``true``, but I like having all of my animation logic in one place. During the scopes conversation, I mentioned that I try to find ways to spend less time reading code. Well if I know that all my animation logic is in one place, then I don't have to read as much whenever I make a animation updates. That's essentially what the Single Responsiblity Principle is (SRP). Every section of your code should be responsible for 1 thing, and it should encompass all of that thing rather than have the logic spread to multiple corners of your game

``image_index + image_speed >= image_number``: This is the pattern I use to detect when I'm at the end of an animation. ``image_index + image_speed`` represents the frame we'll have on the step (similar to a ``x + dx`` check). ``image_number`` is a new variable for us, it represents the total frames in the sprite. So if our next frame exceeds the total frame, then we know we're at the end of the animation, and in this case we want to restart the game

</details>

Next let's update the draw event

<details data-summary="How to update the draw event for death" markdown="1">
```
// oPlayer Draw Event
if(is_dying){
	//// DEATH
	draw_self();
}else{
	//// SET COLOR
	//// ARMS
	//// RESET COLOR
}
```

So if we're dying we just want to use the default draw, rather than go through all the arms logic

</details>

And finally, let's disable control during death

<details data-summary="How to disable controls during death?" markdown="1">

```
// oPlayer Step Event
//// ANIMATION

if(is_dying) exit;

// HORIZONTAL
// VERTICAL
// SHOOTING
```

``if(is_dying) exit;``: Exit is a new keyword for us, it just abruptly ends the event. In this case we essentially want to cancel this event whenever we're dying, so this is a nice quick way to do that

> **Why not not if(not is_dying){ .. } instead of exit?**: Indeed I could have surrounded the existing logic with if(not is_dying) instead of doing exit. I opted for exit because I wanted to teach you something new but I'm conflicted on which one is better 🤓. I used to really like ``exit`` since it was short and quick. Later I thought it might make my code confusing since it's easy to accidentally cancel the wrong code. For example, I was careful to keep the ANIMATION section above the exit, otherwise our animations would have broke. Later on at work I heard about the [keep code left](https://ivan-lim.com/cleaner-code-keep-code-left/) philosophy, which swears by escaping your code early. I suppose they have a point in that your code looks cleaner? Now I do it different depending on the scenario. If it's a clear escape scenario I'll use ``exit``, if there's some logic attached to is_dying, then I'll use a big if statement. If the variable if refactored into state logic (that's the next section), then I essentially never use ``exit;``

</details>

Now when we test it out, the player should actually die (and look still good while doing it 😄)

![](../../images/platformer/player_death.gif)
