---
layout: default
title: Animation Curves
nav_order: 4
parent: Platformer | Camera
---

# Animation Curves

Animation curves were sort of theme in the previous section, so I figured I'd go all the way and give you a idea of how to do the transitions using actual animation curves

## Review

All our camera effects looked very different, but they each had an underlying curve which determined how the animation speed changed. This is often called easing, for example an "ease in" curve starts slow, where as an "ease out" means it ends slow

Let's take a closer look at some of the curves we already coded up. original, current, and dest represent where the value started, where it current is, and where it's going respectively. And the r parameter is a fixed value that influences how quickly the curve completes

| Feature | Code | Description | Image |
|---|---|---|
| Smooth Camera | ``current = lerp(current, dest, r)`` | It get's slow toward the end, so it's ease out | ![](../../images/platformer/smooth_camera_curve.png) |
| Camera Shake | ``current *= r;`` | Also ease out, this is actually a specific case of the previous where dest = 0 |  ![](../../images/platformer/camera_shake_curve.png) |
| Camera Transition | ``t += r; current = lerp(original, dest, t)`` | No ease, this a boring straight line / linear transition. It approaches the destination at the same rate every frame | ![](../../images/platformer/camera_transition_curve.png) |

So the camera transition was kind of boring, and on screen the linear transition looks a little abrupt. Let's fix that

Ideally I'd like to have both ease in and ease out (so start slowly, move quickly in the middle, then end slowly), but tbh I have no idea how to do that with math

Animation Curves to the rescue 🦸‍♂️

## Custom Animation Curves

Go ahead and create a new animation curve, and call it ``animTransition``

When we open it up, and expand ``curve1`` you'll notice that the curve is broken down into a series of points. We can either add them manually, or click/drag on the curve it's self

![](../../images/platformer/add_points_to_curve.gif)

and btw, we can also change the range in case we want to go outside the default 0 to 1 range

![](../../images/platformer/update_curve_range.gif)

We'll just start with a straight line going from (0,0) to (1,1), and come back to update it later

![](../../images/platformer/straight_curve.png)

## Coding

If you type ``animcurve_`` and hit ctrl + space, you'll notice that there are a lot of animation curve function available for us

![](../../images/platformer/animcurve_funcs.png)

Well luckily, if you create the curves in the editor, we only need 2 functions

``animcurve_get_channel``: This will retrieve a specific channel within a curve

**What are channels?**: Well if we go back to the curve editor, we can actually add multiple curves within the channel, and also name them whatever we want. We'll stick with 1 channel for now, and I'll name it "only_channel"

![](../../images/platformer/animcurve_multi_channel.gif)

``animcurve_channel_evaluate``: With this function will can pass in a x value, and we'll get the corresponding y value on the curve (not to be confused with room coordinates, here I'm just saying x/y in the context of the curve)

Now let's see how we can incorporate these into our code 😃

```
// oControl Create Event
// delete trans_amt
trans_cx = 0;

// oControl Step Event
if(is_transitioning){
	//// APPLY TRANSITION ALONG X
	trans_cx += 1 / trans_steps;
	var ch = animcurve_get_channel(animTransition, 0);
	var trans_amt = animcurve_channel_evaluate(ch, trans_cx);
	cam_x(lerp(trans_start_x, trans_target_x, trans_amt));
	if(trans_cx >= 1){
		is_transitioning = false;
	}
}else{
	//// CENTER ON PLAYER
	//// SMOOTH CAMERA
	//// CLAMP TO BOUNDS
	//// CAMERA SHAKE
	//// APPLY
	////TRIGGER TRANSITION TO THE LEFT
	...
		// update trans variables
		is_transitioning = true;
		trans_cx = 0; // replace trans_amt
		trans_start_x = cam_x();
		trans_target_x = left_bounds + max_shake_dist;
	...

	////TRIGGER TRANSITION TO THE RIGHT
	...
		is_transitioning = true;
		trans_cx = 0; // replace trans_amt
		trans_start_x = cam_x();
		trans_target_x = right_bounds - max_shake_dist - cam_w();
	...
}
```

``trans_cx``: This variable represents the x along the curve. I've made ``trans_amt`` a temporary variable, and changed most of the ``trans_amt`` references to ``trans_cx``. We'll step ``trans_cx`` from 0 to 1 the same way we did with ``trans_amt``, but now ``trans_amt`` will correspond to the y value along the curve and that will give us a lot of control over how ``trans_amt`` changes

``var ch = animcurve_get_channel(animTransition, 0);``: ``animcurve_get_channel`` takes to parameters, the curve and the channel. I could have used ``"only_channel"`` for the channel, but we can also use numbers and I figured that's easier when there's only 1 channel. If we had more I would have probably used the name

``var trans_amt = animcurve_channel_evaluate(ch, trans_cx);``: Now that we have the channel, we can pass in the current x along the curve and use it to get the current ``trans_amt``. Again note that I've made it a temprary variable, we'll be retrieve fresh values every step

For now since the curve is just a straight ``y=x`` line the results should be the same. So try testing it out, and make sure it's still working

## More Curve Styles

Now let's get crazier with our curves!

I mentioned we wanted a ease in / ease out effect, well now that's easy. Let's just add a few more points, and see how that looks

![](../../images/platformer/linear_ease_in_out.gif)

It looks better, but it's still a little abrupt. Luckily we can change how the points get shaped. Let's try smooth

![](../../images/platformer/smooth_ease_in_out.gif)

That's it 😀! That's the effect we're looking for! doesn't that look a lot better?

Just to be complete, let's also look at bezier curves. For this we can change the slope at the individual points to have more control

![](../../images/platformer/bezier_ease_in_out.gif)

Also, it's not relevant for this particular curve, but if you hold in alt you can change the left/right sides of the slope

![](../../images/platformer/alt_bezier.gif)

## Presets

So adding points will definitely get us where we want to go, but bezier mode also has a lot of handy presets. We can ease in - ease out to make sure it's following the rough sequence that we want

![](../../images/platformer/animcurve_presets.gif)

if we select "between" instead of "overwrite", we can opt to only apply the preset to subsegments of the curve

![](../../images/platformer/animcurve_between.gif)

Let's try the elastic preset just for fun

![](../../images/platformer/elastic_preset.gif)

It almost gives a cartoony look 🤣, but I think I'll go with the more boring quart option as my final selection

![](../../images/platformer/quart_preset.gif)


## Camera Shake

And that's animation curves! An easy way to adjust how your transitions work w/o doing a lot of math 😍

I mentioned that we used curve logic for the smooth camera as well the camera shake, and we can use animation curves there as well! So if you want to practice maybe you can try replacing the camera shake with animation curves? (smooth camera is a little trickier)

<details data-summary="How would you use animation curves w/ the camera shake?" markdown="1">

Here's my code

```
// oControl Create Event
shake_cx = 0; // remove shake_dist
shake_steps = room_speed;
// oControl Step Event
if(is_transitioning){
	//// APPLY TRANSITION ALONG X
}else{
	//// CENTER ON PLAYER
	//// SMOOTH CAMERA
	//// CLAMP TO BOUNDS
	//// CAMERA SHAKE
	shake_cx = min(shake_cx + 1 / shake_steps, 1);
	var ch = animcurve_get_channel(animShake, 0);
	var shake_dist = max_shake_dist * animcurve_channel_evaluate(ch, shake_cx);
	var shake_dir = random(360);
	var shake_x = lengthdir_x(shake_dist, shake_dir);
	var shake_y = lengthdir_y(shake_dist, shake_dir);
	cam_x += shake_x;
	cam_y += shake_y;

	//// APPLY
	////TRIGGER TRANSITION TO THE LEFT
	////TRIGGER TRANSITION TO THE RIGHT
}

// oTurret Destroy Event
with(oControl){
	shake_cx = 0;
}
```

shake_cx = min(shake_cx + 1 / shake_steps, 1);

``shake_cx``: Similar to the camera transitions, I went through and replace ``shake_dist`` w/ ``shake_cx`` since that's now the primary variable in our shake animation. But ``shake_dist`` went from ``max_shake_dist`` to 0, whereas ``shake_cx`` will go from 0 to 1 just like ``trans_cx`` did

``shake_cx = min(shake_cx + 1 / shake_steps, 1);``: We want ``shake_cx`` to move all the way to 1 and then stop. If you only want to clamp one side, using ``min`` or ``max`` is a handy trick. In this case it will keep stepping upwards until it goes above 1, once it's above 1 (1.1 for example), ``min(1.1, 1)`` would ensure it sticks at 1

For making the curve it's self, I started out with a linear curve going from 1 to 0, and then I applied ease out + bounce (not sure if it's the most natural looking, but it's still fun)

![](../../images/platformer/shake_curve.png)

``var shake_dist = max_shake_dist * animcurve_channel_evaluate(ch, shake_cx);``: I'm having the curve start at 1 and then move to 0. So to incorporate ``max_shake_dist`` I need to multiply it by the curve to make the distance start at ``max_shake_dist`` and then go to 0. I could have also made the curve it's self bigger, but keeping ``max_shake_dist`` as a variable will make it easier to tweak the magnitude of the shake

``with(oControl){ shake_cx = 0; }``: Earlier we had ``shake_dist = max_shake_dist``, but since ``shake_cx`` goes from 0 to 1 we need to start at 0 to initate the animation

</details>

And that should do it, now we can control our camera shake with curves 😍

![](../../images/platformer/camera_shake_with_curves.gif)

## Animation Curve Series

I hope you enjoyed this little detour down curve lane, if you want to try more things I actually made a whole series with different curve effects. It also includes camera transition and shake tutorials, but they use slightly different approaches. For the shake one in particular I use the curve to control ``shake_x`` / ``shake_y`` instead of the ``shake_dist`` so you can check it out to see which approach you like better

<iframe width="560" height="315" src="https://www.youtube.com/embed/LW6Y9_NTdA0&list=PLywxFF6tce1-Y8teVEtN_W9zXJlhGlkH2" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
