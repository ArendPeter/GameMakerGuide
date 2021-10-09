---
layout: default
title: Spotlight
nav_order: 4
parent: Platformer | Spotlight
---

# Spotlight

Last but not least, spotlight!

## Spotlight image

We'll use a large sprite for the spotlight. In this case I'll make it a cirular gradient going from transparent to white (I chose white because it'll be easier to recolor in game maker), you can do this pretty easily in photo editors like paint.net, or photoshop

![](../../images/platformer/paint_gradient.gif)

## Flashlight

When I first used this effect, I thought of it as the player navigating a dark space, and the light sphere around the player represented a flashlight. Let's start with that

```
// oPlayer Draw Event
//// BACK ARM
//// BODY (essengially draw_self())
//// FRONT ARM / GUN
//// SPOTLIGHT
draw_sprite_ext(sSpotlight, 0, x, y, 1, 1, 0, c_black, 1);
```

Another simple effect, that adds a lot to our game!

**Anchoring**: Be sure to center ``sSpotlight`` so that it's aligned properly!

``c_black``: Since the sprite is white, I'm chose ``c_black`` as the blend to make it look dark, and we get a pretty cool effect

![](../../images/platformer/parallax_room_layers.png)

## Other variants

We can try this same effect with lot of different colors and transparencies. Let's try setting the alpha to .1

![](../../images/platformer/parallax_room_layers.png)

Now it's so light that you can barely notice it, but player's will definitely still feel it. Having the center of the screen be brighter draws you attention to the center and it makes the colors seem less flat in general

We can even adjust the colors, we used this effect in Sixit to simulate different times of day (be sure to read up on [make_color_rgb](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Drawing/Colour_And_Alpha/make_colour_rgb.htm) if you want more fine grained color aside from the default colors)

![](../../images/platformer/sixit_gradient_examples.png)

## Fill in sides

This effect mainly works because the spotlight sprite fills the entire screen. Well, that kind of breaks when the player get's near the edges 😬

![](../../images/platformer/broken_spotlight.png)

One way to fix this would be to increase the sprite size, but I better we can do something better. Try using the [draw_rectangle](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Drawing/Basic_Forms/draw_rectangle.htm) function to draw multiple rectangles to fill in the space when the player is at the edge of the room

<details data-summary="How to fill in side with rectangles?" markdown="1">

For this one, I found it helpful to draw out how the rectangles would be setup (I made the spotlight smaller to fit all the possible rectangles, but the math should work with the larger spotlight sprite as well)

![](../../images/platformer/spotlight_fill_reference.png)

```
// oPlayer Draw Event
//// BACK ARM
//// BODY (essengially draw_self())
//// FRONT ARM / GUN
//// SPOTLIGHT
var col = c_black;
var alph = .1;
var spot_width = sprite_get_width(sSpotlight);
var spot_height = sprite_get_height(sSpotlight);
draw_set_color(col);
draw_set_alpha(alph);
draw_rectangle(cam_l(), cam_y(), cam_r(), y-spot_height/2, false); // top
draw_rectangle(cam_l(), y-spot_height/2, x-spot_width/2, y+spot_height/2, false); // left
draw_rectangle(x+spot_width/2, y-spot_height/2, cam_r(), y+spot_height/2, false); // right
draw_rectangle(cam_l(), y+spot_height/2, cam_r(), cam_b(), false); // bottom
draw_set_color(c_white);
draw_set_alpha(1);
draw_sprite_ext(sSpotlight, 0, x, y, 1, 1, 0, col, alph);
```

``sprite_get_width`` / ``sprite_get_height``: Get's the dimensions of a sprite, but keep in mind that this doesn't take into account scaling, so if we were to scale up the spotlight, I need to multiply this by the same scaling

``draw_set_color()`` / ``draw_set_alpha()``: Since ``draw_rectangle()`` doesn't take separate parameters for color, and alpha, we have to set them ahead of time using these functions. I also reset them back to the default values afterwards

</details>

Fixed!

![](../../images/platformer/fixed_spotlight.png)

And with that I think congratulations are in order 🎉

![](../../images/platformer/celebrate.gif)

You just completed the course!! We've got more excersizes coming up, but aside from that you're DONE. Go pat yourself on the back, and have a nice cold beverage of your choosing because you just achieved something big 🤩 (and I'm so proud of you 😭!!)
