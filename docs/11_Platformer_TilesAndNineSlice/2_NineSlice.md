---
layout: default
title: Nine Slice
nav_order: 2
parent: Platformer | Tiles and Nine Slice
---

# Nine Slice

Nine Slice let's you break up your sprites similar to how tilesets break them up, but then it specifically applies to how your sprite is scaled. When you scale sprites, you often want to only stretch the sides while leaving the corners the same. Nine Slice lets you seperate the corners from the sides so that Game Maker will know how to stretch the sprite properly

![](../../images/platformer/nine_slice_example.png)

> Nine Slice are a fairly new addition to Game Maker, it's was made available in the 2.3.2 release in April 2021

## Breaking up the sprite

Jump into the ``sPlatform`` sprite, and then expand the Nine Slice section and check the ``Activate Nine Slice`` button

From there you can adjust the numbers to make the nine isolate the corners. You can use the preview to test

![](../../images/platformer/nine_slice_interface.gif)

You have several options for the tile mode, and you can even specify different ones for each slice. Here's some examples of how they apply in different cases

![](../../images/platformer/nine_slice_tile_examples.png)

## Using Nine Slice in the room

To use nine slice in the room, I'll just create an asset layer to directly place sprites

The nine slice effect will be applied whenever the sprite is scaled. In our case we'll do that using an asset layer, but you can also

Similar to tile and background, this will also be a purely visual layer, so I need to make sure to place corresponding ``oGround`` objects over the platform for it to be interactable

Now when we play the game, you can see it looks *significantly* better 😁

![](../../images/platformer/nine_slice_demo.gif)

> **When should I use tiles vs nine slice?**: You may have noticed that nine slice and tiles have some similarities. They both break up sprites, and if you selected repeat or mirror as your nine slice tile mode, then you could achieve the exact same effect using only tiles. The main benefit of nine slice is convenience. If you need to resize a platform it's much easier to do that using nine slice rather than tile. So use nine slice whenever you can, and fall back to tile sets when you can't
