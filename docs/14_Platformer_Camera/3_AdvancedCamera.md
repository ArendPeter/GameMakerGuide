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

## Camera Transitions

For our next trick let's try adding sliding camera transitions, [here's an example](https://youtu.be/mOTUVXrAOE8?t=166)

We're going to implement this by adding an ``in_transtion`` flag to ``oControl``. Then it can handle the transitioning and follow logic seperately. We'll also add invisible markers to the room. This will be check points where the camera won't cross them unless the player crosses them

First let make an ``oTransMarker`` object, give it the ``sTransMarker`` sprite, and then make it invisible

Then we got to add code. The complete code segment was so big I opted to break it down into separate snippets. Luckily we're near the end of the course, so I know you can handle it 😉

```
// oControl Create Event
left_bounds = 0;
right_bounds = room_width;
with(oTransMarker){
	if(x < other.right_bounds){
		other.right_bounds = x;
	}
}

is_transitioning = false;
trans_steps = room_speed;
trans_amt = 0;
trans_start_x = 0;
trans_target_x = 0;
```


``left_bounds`` / ``right_bounds``: This will be used in our clamping logic. Previously we clamped to be bounded by the room, meaning we clamped on the range ``0``, ``room_width``. With the markers involved, we need to update this depending on the marker position. ``left_bounds`` will be the ``x`` of the marker to the player's left (or ``0`` if there isn't one), and ``right_bounds`` will be the ``x`` of the marker to the player's right (or ``room_width`` if there isn't one)

``with(oTransMarker)``: I'm assuming the the player is starting at the left side of the room (to the left of all ``oTransMarker`` objects). So that means ``right_bounds`` should match the ``x`` of the left most ``oTransMarker`` (which would be the one directly to right of the player). Using ``with`` as a loop is a handy trick for finding the smallest x among the ``oTransMarker`` objects. Every time we find a smaller one we update ``right_bounds`` accordingly. Read over this carefully, since I'll be using a lot of similar ``with`` tricks in this section

``is_transitioning``: This represents whether we're curring in a sliding camera transition. This will complete alter our camera logic in the step event. The following ``trans_`` variables are all used during that transition

``trans_steps``: This is how many steps the transition will take

``trans_amt``: This is how far along we are in the transition. This will always start at 0 and end at 1

``trans_start_x`` / ``trans_target_x``: This is the start and ending x positions of the transition. Note that there's no y because we keep that constant during the transition

```
// oControl Step Event
if(is_transitioning){
	//// APPLY TRANSITION ALONG X
	trans_amt += 1 / trans_steps;
	cam_x(lerp(trans_start_x, trans_target_x, trans_amt));
	if(trans_amt >= 1){
		is_transitioning = false;
	}
}else{
	...
}
```

``if(is_transitioning){``: This ensures that we follow the new camera logic when we're in the middle of transition

``trans_amt += 1 / trans_steps;``: I want trans_amt to go from ``0`` to ``1`` over the course of ``trans_steps`` steps. If we do the math, that means we need to increase it by ``1 / trans_steps`` every step for that to work. To verify I can do ``trans_steps`` steps multiplied by ``1 / trans_steps`` per step, and I get ``1``. Perfect!

``cam_x(lerp(trans_start_x, trans_target_x, trans_amt));``: This is probaly the key line of this segment. We have our starting and endpoint points for the transition: ``trans_start_x`` and ``trans_target_x``, and then we want to transition between them using the ``trans_amt`` variable. It'll start at ``0``, meaning our cam_x will be set to ``trans_start_x``, but by the time ``trans_amt`` reaches 1, we'll be at ``trans_target_x``

>> **I thought lerp had an effect where it get's faster as it get's closer? Is that still happening?**: Ah, no it's not. In the original example, I had the amount locked at ``.1``, and the first number was getting closer to the target every frame. With the range getting smaller and smaller, than meant that the amount of the range was yielding a smaller number as well. This time it's different, the start / ending range remains the same for the entire transition. Also I'm increasing the amount by the same value every frame. So that results in the camera moving at a constant speed throughout the transition

``cam_y``: Note that we didn't have any logic for cam_y. We'll just leave that untouced during this transition

``if(trans_amt > 1){``: This means we've reached the end of the transition, and when this happens we're ready to move back to the normal operations

```
// oControl Step Event
if(is_transitioning){
	...
}else{
	//// CENTER ON PLAYER
	//// SMOOTH CAMERA
	//// CLAMP TO BOUNDS
	cam_x = clamp(cam_x, left_bounds + max_shake_dist, right_bounds-cam_w()-max_shake_dist);
	cam_y = clamp(cam_y, max_shake_dist, room_height-cam_h()-max_shake_dist);

	//// CAMERA SHAKE
	//// APPLY

	//// TRIGGER TRANSITION TO THE RIGHT
	if(oPlayer.x > right_bounds){
		// update bounds
		left_bounds = right_bounds;
		right_bounds = room_width;
		with(oTransMarker){
			if(other.left_bounds < x and x < other.right_bounds){
				other.right_bounds = x;
			}
		}

		// update trans variables
		is_transitioning = true;
		trans_amt = 0;
		trans_start_x = cam_x();
		trans_target_x = left_bounds + max_shake_dist;
	}

	//// TRIGGER TRANSITION TO THE LEFT
	// TODO
}
```

``clamp(cam_x, left_bounds + max_shake_dist, right_bounds-cam_w()-max_shake_dist);``: For this I used to have ``0`` and ``room_width``, but now that we have markers the bounds could be any value, so replaced those with ``left_bounds`` and ``right_bounds`` respectively

``oPlayer.x < left_bounds``: When the player has moved to the left of the camera, that means we're ready to slide the camera to the left

``right_bounds = left_bounds;``: When the camera's done moving, the right side of the view should align with the same marker that's currently on the left side. So we set ``right_bounds`` equal to ``left_bounds``

``left_bounds = 0;``: The camera is about to move left, but we don't know if there's a new marker further left to be a new bound. So I'll start off with 0 by defeault and then we'll increase it if we find new ones

``with(oTransMarker)``: Here we have another ``with()`` loop which is trying to find the largest marker. In this case we start with a range (0, ``right_bounds``), and everytime we find a marker within that range, we update the ``left_bound``. This way we'll have the right most marker within the range by the time we're done, and the ``left_bounds`` and ``right_bounds`` values should correspond to consecutive

``is_transitioning = true;``: This will make the next step follow the transition logic instead of the player following logic

``trans_amt`` / ``trans_start_x`` / ``trans_target_x``: These variables set us up to be used with ``lerp()`` later. ``trans_target_x`` is the most involved. When we go back to to player following mode and we apply the ``clamp`` function, we don't want the view to clamp abruptly. So since the clamp logic has ``left_bounds + max_shake_dist`` set to the minimum, we're going to use that as the transition target before we go back into following mode

When you place a few markers in the room and test it out, we should see the screen shifting when the player walks by the invisible markers. Awesome!

![](../../images/platformer/right_transitions.gif)

``TODO``: But what about the left side?

<details data-summary="How to implement TRIGGER TRANSITION TO THE RIGHT?" markdown="1">
We can largely copy and paste, but with a few critical differences

```
////TRIGGER TRANSITION TO THE LEFT

if(oPlayer.x < left_bounds){
	// update bounds
	right_bounds = left_bounds;
	left_bounds = 0;
	with(oTransMarker){
		if(other.left_bounds < x and x < other.right_bounds){
			other.left_bounds = x;
		}
	}

	// update trans variables
	is_transitioning = true;
	trans_amt = 0;
	trans_start_x = cam_x();
	trans_target_x = right_bounds - max_shake_dist - cam_w();
}
```

``if(oPlayer.x < left_bounds)``: This time we trigger when the player exits to theleft

``other.left_bounds = x;``: When we loop through the trans markers, we're looking for a new left bound this time, so we update this line accordingly

``trans_target_x = right_bounds - max_shake_dist - cam_w();``: Since we're moving to the left, I have to make the ``target_trans_x`` match the maximum in our clampinglogic
</details>

Yay! Now we can do both 🎉

![](../../images/platformer/both_transitions.gif)

## Camera Transition Tweaks

After playing with it a little bit, you may want a few more tweaks. For example, it's kind of annoying when you get shot by missiles from across the room. To fix this let's disable shooting on the missile object when we're in a different area

```
// oMissileTurret Alarm 1 event
alarm[1] = random_range(3*room_speed, 5*room_speed);

if(x < oControl.left_bounds or x > oControl.right_bounds) exit;

...
```

Next I also want to update the player. When we're mid transition I don't think we should be able to move. This is a good opportunity to practice adding another state to the state machine. Want to give it a try?

<details data-summary="How to add a in_transition state to the player's state machine?" markdown="1">

```
// oPlayer Create Event
enum PLAYER_STATE{
	in_control,
	hurt,
	dying,
	in_transition,
}

// oPlayer Step Event
switch(state){
case PLAYER_STATE.in_transition:
	if(not oControl.in_transition){
		state = PLAYER_STATE.in_control;
	}
	break;
case PLAYER_STATE.hurt:
case PLAYER_STATE.dying:
	// do nothing
	break;
case PLAYER_STATE.in_control:
	//// HORIZONTAL
	//// VERTICAL
	//// SHOOTING
	//// TRIGGER IN_TRANSITION STATE
	if(oControl.in_transition){
		state = PLAYER_STATE.in_transition;
	}
	break;
default:
	print("STATE NOT HANDLED", state);
}

// oPlayer Draw Event
switch(state){
case PLAYER_STATE.dying:
	//// DEATH
	break;
case PLAYER_STATE.in_transition:
case PLAYER_STATE.hurt:
case PLAYER_STATE.in_control:
	//// SET COLOR
	//// ARMS
	//// RESET COLOR
	break;
default:
	print("STATE NOT HANDLED", state);
}
```

Disabling the player could have been done in one line, but the nice thing about adding it to a stateis that we need to think through all cases

**When should the player enter the in_transition state?**: Only while the player is in the in_control state, otherwise we might be canceling a death animation

**How should the in_transition state be drawn?**: Like the non-death state, w/ arms and all

**How should the in_transition state animate?**: We shouldn't need to update this, that can follow all the same rules and treat it like an idle scenario

**Whic state should we enter when we exit in_transition?**: definitly in_control. hurt or dying wouldn't make any sense
</details>
