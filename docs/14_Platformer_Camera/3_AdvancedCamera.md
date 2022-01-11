---
layout: default
title: Advanced Camera
nav_order: 3
parent: Platformer | Camera
---

# Advanced Camera

So now we leared about handling our views in code, and how to use custom functions to make our code cleaner, but we've still only recreated what we already knew how to do without code. Let's fix that now

## Smooth Camera

First let's make the camera move smoothly, this will give it a more natural feel

```
// oControl Step Event
//// CENTER ON PLAYER
var target_x = oPlayer.x-cam_w())/2;
var target_y = oPlayer.y-cam_h())/2;

//// SMOOTH CAMERA
var cam_x = lerp(cam_x(), target_x, .1);
var cam_y = lerp(cam_y(), target_y, .1);

//// CLAMP TO ROOM
cam_x = clamp(cam_x, 0, room_width-cam_w());
cam_y = clamp(cam_y, 0, room_height-cam_h());

//// APPLY
cam_pos(cam_x, cam_y);
```

``target_x`` / ``target_y``: I've defined additional temporary variables for ``target``. By setting the center position to ``target_`` instead of ``cam_`` I'm saying that we're not going to reach that position on this frame. Instead we'll work toward the target over several frames, thus creating a smoother effect

``lerp``: This is the power house behind the smooth effect. ``lerp`` stands for linearly interpolate, which is a fancy way of saying mixing/averaging between 2 numbers. It takes 2 numbers (val1, and val2), and then an amount for how much we want to interpolate between those numbers. Still confused? No worries, here are some examples. ``lerp(1, 3, .5)`` would give us 2, because 2 is 50% of the way between 1 and 3. ``lerp(5,10,.2)`` would give us 6 because 20% of the way between 5 and 10. It may seem like a arbitary math function, but once you understand it's power you'll start using it in every corner of your code, it just makes everything look better

``lerp(cam_x(), target_x, .1)``: Here we're using the lerp function to saw "Move 10% from the current camera position to the target camera position". The cool thing about the percentage is the actual distance the camera travels will vary. If the camera is far away from the target then 10% will be a lot, but if it's already close then it'll barely move. This creates a nice easing effect where it moves fast at first, but then slows down as it gets closer to the target. You can tweak the .1 parameter if you want the camera to approach slower or faster


``//// CLAMP TO ROOM``: Note that I was careful in my placement of the clamping logic. We want the camera to slowly approach a target, but if the camera manages to get outside of the room, we want the clamping to be abrupt. So anything that requires an abrupt stop should be applied to ``cam_x`` / ``cam_y`` after the smoothing logic, and everything else should be before

> Quick Rant: I hate that clamp is (val, min, max), and lerp is (val1, val2, amount). They both have take a range, and a 3rd number but they decided to put the 3rd number in different spots and I can never remember which way it is 😭

Go ahead and test it out. What do you think? I really like it, it's shocking how much of a difference that tweak makes. Most players probably won't conciously notice the difference, but I guarantee you they will feel it

![](../../images/platformer/smooth_camera.gif)

> You know how I mentioned that lerp makes everything look nicer? Well if you want more practice you can try applying it to the turret rotation. So if it has a long way to rotate it'll whip around toward the player, but as it's making minor adjustments it'll do that slowly

## Camera Shake

When you manage to kill a turret, that's a big accomplishment. Those turrets are BIG, and they didn't go down without a fight, but when they die they just dissapear? Talk about anti climactic, we can't even tell if they actually died, or if they just whipped out their invisibility cloak. Later we'll use particles to add an satisfying explosion, but while we're covering cameras I think adding a camera shake will also go a long way toward selling that a big thing just happened

```
// oControl Create Event
shake_dist = 0;
max_shake_dist = 32;

// oControl Step Event
//// CENTER ON PLAYER
//// SMOOTH CAMERA
//// CLAMP TO ROOM
cam_x = clamp(cam_x, max_shake_dist, room_width-cam_w()-max_shake_dist);
cam_y = clamp(cam_y, max_shake_dist, room_height-cam_h()-max_shake_dist);

//// CAMERA SHAKE
var shake_dir = random(360);
var shake_x = lengthdir_x(shake_dist, shake_dir);
var shake_y = lengthdir_y(shake_dist, shake_dir);
cam_x += shake_x;
cam_y += shake_y;
shake_dist *= .8;

//// APPLY

// oTurret Destroy Event
with(oControl){
	shake_dist = max_shake_dist;
}
```

``shake_dist``: Every step, after we get a final camera positoin, we're going to shift it in a random direction to create the shake effect. The ``shake_dist`` represent the distance we shift it, and this will determine how severe the camera shake is

``max_shake_dist``: This is the maximum camera shake. When something big happens the ``shake_dist`` will start at ``max_shake_dist`` and then it'll go down from there

**Destroy Event**: This is triggered when an object is destroyed, so when the turret's destroyed we have it trigger the camera shake by setting the ``shake_dist``

``//// CLAMP TO ROOM``: I updated the clamping logic slightly. Since we're applying the camera shake after the clamping, we want to avoid a scenario where the shake causes the camera to go outside the room so we're clamping it within a margin of the edge. If the minimum camera x is at ``max_shake_dist``, then we can guarantee that the camera shake won't be able to move the view outside the room

``shake_x`` / ``shake_y``: These represent the shift caused by the shake this frame. We start with a random direction, ``shake_dir``, then use ``lengthdir_x`` / ``lengthdir_y`` to convert the polar form ``shake_dist`` / ``shake_dir`` over to the cartesian ``shake_x`` and ``shake_y``

``shake_dist *= .8;``: Multiplying the distance by 80% will reduce the distance by 20% every frame. This has a similar effect to lerp where it will reduce quickly at first, but it'll take a while for it to fully die. ``lerp(shake_dist, 0, .2)`` would have also worked for this situation but since since we're approaching 0 I figured a simple multiple would be easier

Here's what it looks like when we kill the turrets now 💥

![](../../images/platformer/screen_shake.gif)
