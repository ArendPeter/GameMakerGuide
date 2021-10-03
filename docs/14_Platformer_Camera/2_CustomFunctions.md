---
layout: default
title: Cameras
nav_order: 2
parent: Platformer | Polish
---

# Cusotm Functions

So now we've learned camera coding to the point where we've caught up with what we could do without code. I want to show you more cool camera stuff, but first I need to get something off my chest

## Shorter Cameras

Quick history lesson: Did you know that ``camera_set_view_pos(camera, x, y)`` wasn't always the goto for updating the cameras position 😲! Up until the release of Game Maker Studio 2 in 2016, game maker let you adjust the vew position by adjusting the variables ``view_xview`` and ``view_yview``. It was a much more convenient time. The GMS2 system let's you do a lot of new neat things with cameras, but for a lot of people it was overkill for what they needed. So a lot of cranky old school people decided to write their own functions to recapture the glory of the ``view_xview`` days, and that's what we'll do here

To start, create a new script and call it ``ShorterCamera``. Scripts contain code that get's ran at the start of the game, but the most popular usage is to define common custom functions (you can also define functions in the objects, but if you want them available globally you'll need to do it in a script)

When I initially showed you game maker functions, I mentioned that they do 3 things

1. Takes inputs
2. Performs an action
3. Gives back outputs

I'll go over how to handle each of those on your own

## Getter Functions

We'll start with a function that just gives you the x position of the camera . Add this to the script

> **What's a getter?**: Functions that just give you back something without making any changes are called getters. Similarly, functions you use to change variables are called setters

```
function cam_get_x(){
	return camera_get_view_x(view_camera[0]);
}
```

``function cam_get_x(){..}``: When you make your own functions, you start with a line giving the function definition. In this case that's just ``function`` and then the function name along with the function `()`. Everything that the function actually does get's defined within the ``{}``

``camera_get_view_x(view_camera[0])``: This gives us the x position of the camera. I'm doing ``view_camera[0]`` because 0 is what most people will want anyway, and this way we can have one less parameter

``return``: This is used for the "Gives back outputs" part of the function. Once you have the final result for the function you can just do ``return result;`` and then that will be the end of the function

To test this out, try adding ``print(cam_get_x());`` to ``oControl`` and see if the console output makes sense

If it does, then congratulations, you've just made your first function 🎊

## Setter Functions

Now let's try making a similar function for changing the x

```
function cam_set_pos(xx, yy){
	camera_set_view_pos(view_camera[0], xx, yy);
}
```

``(xx, yy)``: This is how you define the "Takes inputs" part of the function. You can setup a series of variables (separated by commas) here, and the inputs will be stores as temporary variables depending on the parameter names

Now you can try something like ``cam_set_x(100);`` and it should update your camera position acordingly

## Optional parameters

You may have noticed that some of game makers function can support multiple number of parameters. For example, ``choose()`` can handle ``choose(1,2)`` as well as ``choose(1,2,3)``. Instead of having getters and setters, let's try making general ``cam_x()`` and ``cam_y()`` functions which can do either

```
function cam_x(xx){
	if(xx != undefined){
		cam_set_pos(xx, cam_y());
	}

	return camera_get_view_x(view_camera[0]);
}

function cam_y(yy){
	if(yy != undefined){
		cam_set_pos(cam_x(), yy);
	}

	return camera_get_view_y(view_camera[0]);
}
```

``xx != undefined``: If we call ``cam_x()`` without parameters, then game maker will still give you the ``xx`` variable, it'll just use ``undefined`` as the value. This gives us an easy way to handle multiple parameters while just writing the functions once (looking at you Java)

``cam_set_pos()``: Since there's no ``camera_set_view_x()`` function I'm calling ``cam_set_pos()`` together with ``cam_y()`` (or ``cam_x()`` in the case of the ``cam_y()`` implementation), to get the current value for the other variable

Now you you should be able to do ``cam_x(100);``, and then also do ``cam_x()`` to show that both usages work

## Practice

I have a script with all the camera functions I might want, [here](https://gist.github.com/ArendPeter/6958b62c29e1d430a9c8d82fd949e0cd), but before you read that, here are some you can implement on your own first as practice

``cam_get_w()``: A getter for the view width
``cam_set_h(hh)``: A setter for the view height
``cam_w(ww)``: Takes ww as an optional parameter and handles getting and setting
``cam_get_r()``: A getter for the right side of the view
``cam_get_center_y()``: A getter for the y position of the center of the view

Once you've done those you can either copy my script, or try implementing the rest of the functions from that script

## Code Rewrite

Now let's see how our code changes with the new functions. You can give it a try on your own first

Here's the original for reference

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

<details data-summary="How to update the code w/ shorter camera functions?" markdown="1">

```
// oControl Step Event
//// CENTER ON PLAYER
var cam_x = oPlayer.x-cam_w()/2;
var cam_y = oPlayer.y-cam_h()/2;

//// CLAMP TO ROOM
cam_x = clamp(cam_x, 0, room_width-cam_w());
cam_y = clamp(cam_y, 0, room_height-cam_h());

//// APPLY
cam_pos(cam_x, cam_y);
```

Now that's a big improvement if you ask me 😍

</details>

> **Why did game maker make the camera system so complicated 😭?** I don't mean to hate on the new Game Maker camera system. The new system added support for a lot of cool use cases, and it was smart of them to add the ``_view_`` function for people who didn't need all that power. Adding further simplifications to GML would have probably been confusing, so letting us make our own short functions is probably for the best
