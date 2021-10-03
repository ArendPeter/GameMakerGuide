---
layout: default
title: Camera
nav_order: 1
parent: Platformer | Camera
---

# Cameras

Let's learn how to do cameras w/ GML 📷

## Terminology

First let's go over some terminology. I'm just going to plagarize directly from the [game maker documentation](https://docs2.yoyogames.com/source/_build/3_scripting/4_gml_reference/cameras%20and%20display/cameras/index.html) for this one

**PLAGARISM BEGIN**

**The Camera**: A point within the room that will be used to set how the room is displayed - typically with position, orientation, field of view and aspect ratio
**The View**: What the camera sees, based on the position, projection and rotation of the camera
**The View Port**: The area of the screen where the camera view will be displayed

![](../../images/platformer/camera_example.gif

**PLAGARISM END**

So, the true **Camera** actually exists in 3d space, and the **View** is the 2d image that the camera sees, and the **View Port** is the section of the window where the view will be shown. By default the view port covers the whole window, this is almost always what you want (unless you're making a split screen multiplayer game)

We're mainly interested in the view position (afterall that's what we were adjusting in the room editor), and there's 2 ways to adjust the view position

1. Adjust the camera by manipulating it's view matrix ([docs](https://docs2.yoyogames.com/source/_build/3_scripting/4_gml_reference/cameras%20and%20display/cameras/index.html))
2. Use the ``_view_`` functions to adjust the camera directly

I'm going to going with option 2, officially because I think they'll be more relevant to you, but unofficially because it's the only method I've ever used and option 1 still scares me a little. Shaun Spalding has a great video with more camera system details [here](https://www.youtube.com/watch?v=rrn_2hxL11A)

## Setup Room

We're going to repeat some of the steps from "FlappyBird | Camera". Resize the room to make it wider, and taller (we'll see why later), then populate it accordingly. Also you'll still need to enabled view and view ports in the settings, but the similarities stop there (i.e. don't set view following, or view speed, we'll do that in code). You could enabled views / view ports in code, but I usually just do it in the room (it pains me to say it sometimes it's just easier use UIs rather than code 😭)

Here's what I got after making the update

![](../../images/platformer/wide_room.png)

## Screen Follow

Now let's implement the same object following feature but in code. Create a new ``oControl`` object, add it to the room, and then add the following to the step event

```
// oControl Step
//// VIEW FOLLOW PLAYER
{
	camera_set_view_pos(
		view_camera[0],
		oPlayer.x-camera_get_view_width(view_camera[0])/2,
		oPlayer.y-camera_get_view_height(view_camera[0])/2
	);
}
```

``camera_set_view_pos``: This let's you set the top left corner of the camera, which pretty much let's you move it wherever you want

``view_camera[0]``: Sometimes you want to have multiple cameras/views (usually for splitscreen), so here we're saying that we're adjusting the view for camera \#0. This ``view_camera`` variables is actually an array (just like the ``alarm`` variable). Arrays are great when you have a series of similar variables. It's pretty much the same as if you made a bunch of variables named ``view_camera_0``, ``view_camera_1``, etc. Except you use the ``[]`` syntax. Arrays get you other benefits but that's out of scope for the course


``camera_get_view_width(view_camera[0])``: This gives you the width of the view. Again, we specify the view for camera \#0 because there can be multiple cameras

``oPlayer.x-camera_get_view_width(view_camera[0])/2``: To make the camera follow the player, I'm essentially repeatedly updating the camera position to ensure that the player is always at the center of the screen. That means that half the camera view is to the left of the player, and the other half is to the right. So the left side of the camera is at ``oPlayer.x - camera width / 2``

![](../../images/platformer/camera_center_math.png)

``oPlayer.y-camera_get_view_height(view_camera[0])/2``: Pretty much the same reasoning as with x, it's just using the view height function instead for view width

> **Why are you using ``oControl`` for this and not the player**: 🤓 Camera logic can get complicated, and following the Single Responsibility Principle (SRP), I figured the player already has more than enough to worry about. We can have ``oPlayer`` focus on figuring out it's own position, and then ``oControl`` figure out the camera stuff from there. Controllers objects are great for code that doesn't have an obvious home in any specific object, sometimes it even get's cluttered, and I split it into multiple controller objects (ex. ``oCameraControl``), but just one should be good enough for now

When you test this out, you'll see the player is centered on the view 😀. But Game Maker almost followed our intructions too well, since it let's us see outside of the room, 🤔

![](../../images/platformer/camera_center_outside_room.gif)

## Bound view to room

Some games don't actually care about room edges (like if you were making terraria or minecraft, maybe you'd just want to keep making more things outside the room, and you could do that), but for our game we do, so let's set some limits on the view position

```
// oControl Step Event
//// CENTER ON PLAYER
var cam_x = oPlayer.x-camera_get_view_width(view_camera[0])/2;
var cam_y = oPlayer.y-camera_get_view_height(view_camera[0])/2;

//// CLAMP TO ROOM
cam_x = clamp(cam_x, 0, room_width-camera_get_view_width(view_camera[0]));
cam_y = clamp(cam_y, 0, room_height-camera_get_view_height(view_camera[0]));

//// APPLY
camera_set_view_pos(view_camera[0], cam_x, cam_y);
```

``var cam_x``: The centering logic is actually the same, I'm just using some intermediate temporary variables so that we don't have the bury all the math in a single function call

``clamp()``: This is a handy function, I give it a number as well as a minimum and maximum for the number. Then the function will either give back the same number or give one of the limits if the number was outside of the minimum/maximum. For example ``clamp(3, 0, 5)`` would return ``3`` because it's within the 0 to 5 range. But ``clamp(6, 0, 5)`` would return 5 because 6 is outside of the range, and it get's capped at 5

``0``: To keep the view within the room, the smallest possible position for the top left corner is (0, 0). So that's why we use 0 as the minimum number in our clamp

``room_width-camera_get_view_width(view_camera[0])``: The maximum is a bit more complicated. We essentially don't want the right side of our view to be bigger than the room_width, so I guess the rule would be ``right side < room_width``. But the right side is just the left side of the view width added on, so this statement should be identical ``left side + view width < room_width``. And finally if we do math and take the width from both sides we end with ``left side < room_width - view width``, and that's how we got ``room_width-camera_get_view_width(view_camera[0])`` as our maximum for the view position.

![](../../images/platformer/camera_room_math.png)

``cam_y = clamp(cam_y, 0,
room_height-camera_get_view_height(view_camera[0]));``: All the logic should be
the same for y

Cool, so now our logic starts with centering the player on the view, but then it adjusts the view to stay in the room

![](../../images/platformer/camera_center_inside_room.gif)
