---
layout: default
title: Parallax
nav_order: 3
parent: Platformer | Parallax
---

# Parallax

We covered parallax a little during flapy bird. Remember how much cooler it looked with the moving background? Well we're going to do the same here, it's just going to be a tad more complicated

## Adding Layers

We've got a bunch of assets to use as parallax layers. Go ahead and add the following asset layers, and then populate them with the corresponding

 * "Mountains": This will be very far away
 * "Trees": This will be a medium distance away
 * "Lights": This will be close, it should look like beams of light coming from roughly above the player
 * "Lights2": This will be even closer, same assets as the previous layer, but having 2 will create a nice overlapping effect
 * "Vines": This will be the foreground, actually closer to the camera than the player is

 Here's what my room looks like after adding those assets (hopefully the layer order will be intuitive, but you can reference mine if you need help)

![](../../images/platformer/parallax_room_layers.png)

## Implementing Parallax

Now that we've got layers of varying distances, let's make them move in a parallax way!

This is one of those cases where there's a lot of different techniques you can use to do the same thing. Every few years I find a new favorite technique, here's my current favorite

```
// oControl Create Event
//// BACKGROUNDS
{
	bkg_mountains = layer_get_id("Mountains");
	bkg_trees = layer_get_id("Trees");
	assets_vines = layer_get_id("Vines");
	bkg_lights = layer_get_id("Lights");
	bkg_lights2 = layer_get_id("Lights2");
}

//// CONTROL STEP

// PARALLAX
{
	layer_x(bkg_mountains, cam_x());
	layer_x(bkg_trees, cam_x()*.5);
	layer_x(bkg_lights, cam_x()*.3);
	layer_x(bkg_lights2, cam_x()*.2-200);
	layer_x(assets_vines, cam_x()*-.5);
}
```

``layer_get_id("Mountains");``: When referencing layers in code, you can either use a string or the layer id. It's more convenient to use a string, but Game Maker can update the layers a little faster if we give it the id instead of the name. Since we're updating the layers on every step, I'm looking up the ids in the create event in order to help speed up the step event. It's probably a minor performance tweak, so using the strings in the step event is probably also fine

``layer_x(..);``: This function lets you take a layer and specify it's new x value. By default the layer's x value is 0, and if you changed it to 5 (for example), it would move everything in the layer 5 pixels to the right. This feature makes it really easy for us to shift the layers for our parallax logic

``layer_x(bkg_mountains, cam_x());``: The mountains are supposed to be really far away, so if the player is moving to the right, the mountains should follow the camera and appear to be unmoving in the horizon. I use ``cam_x()`` here because we want it to stay aligned with the left side of the view at all times


``layer_x(bkg_trees, cam_x()*.5);``: The trees are much closer than the mountain, but still further than the turrets. The layer with the turrets and the other instances will always have an x position of 0, so to put the trees between the instances and the mountain I do the average and get ``cam_x()*.5``

``layer_x(bkg_lights, cam_x()*.3);``: We can play with that factor however we want. If it's close to 1, it'll look like it's very far off in the distance, and if it's close to 0 it'll be appear closer to the player. Since I want the lights to appear in front of the trees, I'll use ``.3``

``layer_x(bkg_lights2, cam_x()*.2-200);``: And these lights are closer still. I added the ``-200`` to show you more options for adjusting the parallax. Adding shifts is just another option for adjusting the effect

``layer_x(assets_vines, cam_x()*-.5);``: You can actually bring the factori below 0 😮. Since we want the vines to look like they're really close to the camera, we can use a negative factor to make it appear even closer than the player

Once we've got that all implemented our game should look almost 3D 👓

![](../../images/platformer/parallax.gif)
