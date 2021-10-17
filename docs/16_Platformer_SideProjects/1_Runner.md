---
layout: default
title: Runner
nav_order: 1
parent: Platformer | Side Projects
---

# Runner

## Where do start?

<details markdown="1">
1. Create objects (and sprites) for ``oPlayer``, ``oWall``, and ``oSpike``
1. Update ``oPlayer`` to move horizontally (constant ``dx`` + horizontal collisions)
1. Update ``oPlayer`` to move vertically (jump + gravity + vertical collisions)
1. Update ``oPlayer`` to die when they hit spikes
1. Update camera to follow the player (I have the camera offset so that the player can see more ahead)
</details>

# How do I make the rotation effect?

<details markdown="1">
The easiest way is probably to increase ``oPlayer``'s angle when they're above ground, and lock to 0 when they're on the ground

That gets tricky, because if you get the rotation speed wrong the ``oPlayer`` can hit the ground mid rotation, and there'll be a jarring snap to 0

I probably over-engineered this but, I ended up determining the angle based on the current player's speed. Let's say our jump speed is 10. That means ``dy`` is -10 at the start of the jump, 0 at the top, and then (assuming flat ground), it'll be 10 again at the bottom

So with that knowledge I can create a variable to represent how far along I am through the jump (again assuming flat ground): ``var t = (dy + abs(jump_dy)) / (2*abs(jump_dy))`` (this is "current + start / (end - start)", and it'll give me a number 0 to 1 representing how far between current and start I am). From there I did ``angle = lerp(90, -90, t)``, note that dy = 0 maps to angle = 0, so that should give me the correct angle even when I'm not jumping

Extra tip: By default game maker will factor image_angle into your collisions, but you probably want to keep your collisions consistent. To handle this I kept ``image_angle`` at 0, instead used a separate variable together with ``draw_sprite_ext(..)``
</details>

# How do I detect collisions w/ the side of the wall?

<details markdown="1">
I went over this when covering generic bounce in breaker, but now that we know collision functions it's much easier. You can use ``place_meeting`` (or ``place_free``, as well as some others) to only preview collisions using ``dx`` or ``dy``

```
if(place_meeting(x+dx, y, oSolid)){
	// horizontal
}

if(place_meeting(x, y+dy, oSolid)){
	// vertical
}
```

</details>

# I feel like the spikes kill me when they're not supposed to 😢

<details markdown="1">
Ah, if you go into the sprite and look at the collision mask, you'll notice that it uses the entire square as a collision box by default. So if the player clips the corner of the collision box they'll die without actually hitting the spike art and it won't seem fair

![](../../images/platformer/pre_spike_collision_box.gif)

You could make it precise collision checking, but I opted to keep it a square and make it smaller than the spike art (people only complain about the game cheating when it hurts them 😉)

![](../../images/platformer/post_spike_collision_box.gif)


There's actually a lot of cases where games bias their systems to keep player's happy. For example, if a game tells you something is 90% accurate, it's actually probably even more accurate. Otherwise players will feel like it's unfair ([link](https://youtu.be/dwI5b-wRLic?t=900))

</details>

# Sometimes my jump presses get ignored 😭

<details>
There's a couple of reasons this could happen, but one frustration I had is that I often hit the jump button before hitting the ground and the game would ignore it because it's an air jump

This behaviour is technically correct, but it doesn't feel good, so let's cheat the game system a bit

This hack is called jump buffering. When the player hits the jump buffer, we store the key press for a few frames, and then trigger a jump if they hit the ground within that period

```
if(keyboard_check_pressed(vk_space)){
	jump_buffer = max_jump_buffer;
}

if(not place_free(x, y+1) and jump_buffer > 0){
	dy = jump_dy;
}

jump_buffer -= 1;
```

In this case I have ``max_jump_buffer`` set to ``10`` frames, but that can be adjusted. I double check [Sixit](https://stargardengames.com/sixit) and [Binary Dash](https://stargardengames.com/binary-dash), and those use jump buffers of ``8`` and ``6`` respectively (wow, everything is 6 in Sixit, that one wasn't even on purpose 😲)

Maddy Thorson (creator of Celeste) has a [good twitter thread](https://twitter.com/maddythorson/status/1238338574220546049) covering jump buffering, and all the other platforming hacks they have in their game

</details>
