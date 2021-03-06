---
layout: default
title: Particles
nav_order: 1
parent: Platformer | Polish
---

# Particles

Particles are everywhere, rain, smoke, dust, clouds, and more! Adding them to your game will make it look more natural and polished.

Let's learn about particles!

## Particle Basics

**What is a particle?**: These are little dots (or other shapes), that fly around on the screen. They don't interact with anything on the screen, they're mostly just used for adding visual flare

There are 3 components that go into particles

 * **Particle System**: This has properties for a group of particles (for example depth)
 * **Particle Type**: Before you make particles you need to define a particle type. This will have all the information about how the particles look and behave
 * **Particle Emitter**: These emit the particles. Everything about how are where the particles spawn is determined by the emitter

 TODO: do I need to talk more about depth / layers at some point?

Sweet, next let's see how this translates to code

## Turret Explosion

We already added camera shake to make the turret death pop more, let's ad particles for even more pop 💥

```
// oTurret Create
//// PARTCILES
{
	p_sys = part_system_create();

	p_type = part_type_create();
	part_type_shape(p_type, pt_shape_smoke);
	part_type_color1(p_type, c_gray);

	p_emit = part_emitter_create(p_sys);
	part_emitter_region(p_sys, p_emit, x-10, x+10, y-10, y+10, ps_shape_ellipse, ps_distr_linear);
}
```

``p_sys = part_system_create();``: This creates a new particle system

``p_type = part_type_create();``: This creates a new particle type

``part_type_shape(p_type, pt_shape_smoke);`` / ``part_type_color1(p_type, c_gray);``: These define the shape and color properties for the particle. There are a LOT of particle properties, it's not uncommon to have to call 10-15 property functions after creating your particle type. They all start with ``p_type`` to indicate which particle type is being updated, and then they follow up with the property specific parameters. ``ctrl + space`` and ``F1`` are your friends. Type ``part_type_`` and ``ctrl + space`` to see the full list of particle type functions. Also you can hit ``F1`` on any of them to see the documentation. Try clicking ``pt_shape_smoke`` and hitting ``F1``, this will show you all your other particle shape options

``p_emit = part_emitter_create(p_sys);``: Creates a new particle emitter. Be sure to include the system here as one of the input parameters

``part_emitter_region(p_sys, p_emit, x-10, x+10, y-10, y+10, ps_shape_ellipse, ps_distr_linear);``: This defines where the particles will spawn. You start with 2 points to form a rectangle, and then you specify the shape an distribution. In this case I'm putting ``ps_shape_ellipse`` so that it'll limit to the circle within the defined rectangle, and ``ps_distr_linear`` to indicate that I want them to spawn evenly across the area (as opposed to ``ps_distr_gaussian`` where most particles will spawn toward the center of the emitter region)

That's all for the setup, but it won't spawn any particles unless we call either the ``part_emitter_burst`` function, or the ``part_emitter_stream`` functions. Let's do that next

```
// oTurret Step
{
	if(visible){
		if(hp < 0){ // add visible afterward
			visible = false;
			part_emitter_burst(p_sys, p_emit, p_type, 10);
			alarm[1] = -1;
			with(oControl){
				shake_cx = 0;
			}
		}
	}else{
		if(part_particles_count(p_sys) == 0){
			instance_destroy();
		}
	}
}

// oTurret Destroy
delete this event, we don't need it anymore
```

``if(visible)``: I'm updating the turret to handle it's death in 2 phases. First it'll go invisible, where it spawns a bunch of particles, and triggers the camera shake. Then once the particles are all gone, we'll actually destroy the turret. I use the ``visible`` variable to figure out which phase we're one. If ``hp < 0`` then we're ready to start the first phase, and ``visible`` will be set to false. Then while ``visible`` is false, we'll be checking to see if the particles are complete

> **Why are we using ``visible`` to store state? Didn't you spend a lot of time talking about flags and state machines for this kind of thing?**: Yep you got me, in this case ``visible`` is the easy/hacky way, but using flags or a state machine is probably better. I guess I can get a way with it since the turret object is a little simpler than the player, but I'm also just being kind of lazy

``part_emitter_burst(p_sys, p_emit, p_type, 10);``: Here we send out a burst of 10 particles (with the system, emitter, and particle type that we defined). This emits all 10 particles at the same time, if we had used ``part_emitter_stream`` then it would have continued to send out 10 particles every step until we changed it. Since we want this to be a single explosion, ``part_emitter_burst`` is the way to go

**Particle Independence**: One important note is that particles systems/types/emitters all exist completely independently from the object. We create the particles from the object, but they aren't affected by the objects properties. This is why we can make the object invisible without affecting the particles. Changing visible just disable's the objects draw event, but the particles are drawn separately. We'll find later that the particles don't even care if the object exists or not, we can destroy the object, or even restart the game and the particles will just continue to exist. I'll cover how to handle this a bit later

``alarm[1] = -1;``: Since the turret is now in the dying phase, we want to disable the shooting alarm

``if(part_particles_count(p_sys) == 0){``: This checks how many particles are left in the system. Once you create particles, they move around for a bit, and then eventually die. You can change how long particles last using ``part_type_life`` but by default they only last for a few seconds

Nice work! Now we should see an explosion when we kill turrets

![](../../images/platformer/turret_explosion_v1.gif)

Well it still needs some work. Maybe you could try browsing through the particle properties, and try making it look better?

<data-summary="How to improve the particles?" markdown="1">

Here's what I came up with, but there are many right ways to do this, so don't take this as gospel

```
p_type = part_type_create();
part_type_shape(p_type, pt_shape_smoke);
part_type_color1(p_type, c_gray);
part_type_alpha2(p_type, 1, 0);
part_type_life(p_type, room_speed, 2*room_speed);
part_type_speed(p_type, 1, 2, -.1, 0);
part_type_direction(p_type, 0, 360, 0, 10);
part_type_size(p_type, .5, .7, .05, 0);
```

Here's what I came up with after play with it a bit. When working with particles it's pretty common to do a lot of iterations of testing the game and then going back to tweak the values. Don't feel like you need to know the numbers right away, it always takes some tweaking


``part_type_alpha2(p_type, 1, 0);``: This sets the alpha/transparency of the particle type. We use ``alpha2`` to indicate that we want to specify 2 alpha values (one for the beginning, and one for the end). In this case I'm having the particles start at ``1``, and then fade to 0 by the end of the particle's life

``part_type_life(p_type, room_speed, 2*room_speed);``: I want to prolonge the fading effect, so I'm changing the particle life. Here I give it a minimum and a maximum range so they could die anywhere from ``1`` to ``2`` seconds. Now in addition to fading, we'll also see the particles thining out since most of them will already be gone by the time we hit 1.8 seconds

``part_type_speed(p_type, 1, 2, -.1, 0);``: At the moment the particles just sit there, but with an explosion you'd expect them to move away so I'm adding a bit of speed. The first to properties are the minimum and maximum starting speed, so their speeds will range from ``1`` px/step to ``2`` px/step. This will give them a spread as some will end up traveling further than others. I also wanted to particles to slow down as they move away from the start (this looks more naturaly since real smoke would be slowed down by air resistence), so I specify ``-.1`` to be the increment. This indicates that the speed should decrease by .1 every step. The last parameter is the wiggle, this will randomly change the speed over time. I didn't feel like I needed that one

``part_type_direction(p_type, 0, 360, 0, 10);``: In addition to speed I also wanted to clarify which direction it should move. The parameters are very similar to the ``part_type_speed`` parameters, with min, max, increment, and wiggle. I wanted to initial direction to be completely random between ``0`` and ``360``. I didn't want an increment (although adding increments to direction creates a cool spiral effect, definitely worth trying at some point). And then I used ``10`` for the wiggle to hopefully make it look a little more natural. So it's essentially adding ``random_range(-10, 10)`` to the direction on every step

``part_type_size(p_type, .5, .7, .05, 0);``: Size also follows the min, max, increment wiggle pattern. By default the particles were a tad big, so I changed the sizes to range between ``.5`` and ``.7``. I'm actually having it grow over time, combining this with the decreasing transparency makes it look like the smoke is dissapating

</data-summary>

Now we've got an awesome explosion for our turret 😎

![](../../images/platformer/turret_explosion_v2.gif)

## Flames

Next let's use particles to make the spark bullets actually look like flames

We'll start by unchecking the visible box for ``oSpark``

This is a common approach for simulating fire, water, and other effects. Just make actual bullets to handle the collisions and then use particles to make them look pretty

Here's what I came up with 😜

```
//// oFlame Create
// particles
{
	p_sys = part_system_create();

	p_type = part_type_create();
	part_type_shape(p_type, pt_shape_explosion);
	part_type_color_mix(p_type, c_red, c_yellow);
	part_type_life(p_type, 5, 15);
	part_type_alpha3(p_type, 0, 1, 0);
	part_type_size(p_type, .75, 1, -.01, 0);

	p_emit = part_emitter_create(p_sys);
	part_emitter_region(p_sys, p_emit, x-8, x+8, y-8, y+8, ps_shape_ellipse, ps_distr_linear);
	part_emitter_stream(p_sys, p_emit, p_type, 2);
}

//// oFlame Step
// particles
{
	part_emitter_region(p_sys, p_emit, x-8, x+8, y-8, y+8, ps_shape_ellipse, ps_distr_linear);
}
```

``part_type_color_mix(p_type, c_red, c_yellow);``: This set the particle color to be a random mix between 2 colors. So the particles can be red, yellow, orange or any other color in between

``part_emitter_region(p_sys, p_emit, x-8, x+8, y-8, y+8, ps_shape_ellipse, ps_distr_linear);``: The region roughly matches the shape of the bullet. I've also added it to the step event, that way the particles will continue to spawn from the correct location as the spark moves

``part_emitter_stream(p_sys, p_emit, p_type, 2);``: I'm using stream here because I want the particles to keep spawning as the bullet moves

![](../../images/platformer/flame_particles.gif)

## Particle Clean Up

Particles can be a little stubborn about going away sometimes. Let's add a debug key for restarting the game, and I'll show you what I mean

```
// oPlayer Step Event
switch(state){
	...
}

//// RESTART DEBUG KEY
if(keyboard_check_pressed(ord("R"))){
	game_restart();
}
```

Now trigger the flame turret, and then restart the game

![](../../images/platformer/particle_clean_up_bug.gif)

What!?! 😲 Why are the particles still there!!! 😭

So as I mentioned earlier, particles are independent of objects, and not just that, they're independent to the point where it's kind of extreme. Doesn't matter if destroy the object, go to a different room, or even restart the game. The particles will hang around in memory until you explicitly delete them

Early on, you had to think of all these scenarios when handling particle clean up, so we'd have the same clean up code in the destroy event, room restart event, game restart event, etc. Now, luckily, Game Maker added a clean up event to help with this

```
// oTurret Clean Up Event
part_type_destroy(p_type);
part_emitter_destroy(p_sys, p_emit);
part_system_destroy(p_sys);

// oSpark Clean Up Event
part_type_destroy(p_type);
part_emitter_destroy(p_sys, p_emit);
part_system_destroy(p_sys);
```

Now when you try the same trick, the particles should clean up properly

This bug is very easy to miss, I think I've hit it in every game I've made, and it's often pretty late in development

![](../../images/platformer/sixit_tunnel_bug.png)

## More Particles?

I'll chill w/ the particles for now, but you can keep going as long as you like 😉, here are some other options if you're interested

 * Smoke on the missile
 * Bullet Collision
 * Rain

> **Can you have too many particles?** Technically yes, with enough particles you'll eventually hit performance issues, but designwise not really. If you have a lot of big effects your player will get really overwhelmed, but if you look for it, you'll find that modern games have tiny particles effects EVERYWHERE. It's just a great tool to make your world seem more real and alive

## Particle Tools

As mentioned earlier, whenever I make particles there tends to be a lot of back and forth rerunning the game to make sure the effect looks the way I want it

There are tools out there which let you preview particles in real time. In theory I think they'd save a lot of time, and are probably worth using

I haven't gotten around to using them myself yet (old dog, new tricks y'know), but don't let me hold you back! Here are some optoins to consider

* [Particle Forge by DragoniteSpam](https://dragonite.itch.io/game-maker-particle-forge): This is a free tool from the Game Maker community (but please donate if you can)
* [GeonFx](https://marketplace.yoyogames.com/assets/4574/geon-fx-particle-editor): This seems like a really professional one, but it also comes with a price tag
