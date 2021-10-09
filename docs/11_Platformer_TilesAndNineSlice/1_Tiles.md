---
layout: default
title: Tiles
nav_order: 1
parent: Platformer | Tiles and Nine Slice
---

# Tiles

In this section we'll go over Game Maker's tile system. Tilesets are essentially just fancy backgrounds, but they're a great way to make your rooms looks good without making a bunch of custom art

We start with environment art that can be broken up into squares (or rectangles), and then we can place the pieces into the room the same way as we would place tiles on a bathroom floor

## Tileset creation

Right click on the tilesets folder and select ``Create > Tile Set`` as you would with other Game Maker resources

Then within the tileset properties window you can select the tile set sprite (we'll use ``sTileset``). Now we see a bunch of numbers, this will let us indicate how we'd like to break up our sprite into tiles. For our case I ended up using these numbers

![](../../images/platformer/tiles_width_height.png)

The 3 main number categories are, width & height, offset, and separation. There's more they won't be needed in 98% of cases so I'll leave them off for now (and tbh, I've never needed to use them)

### Width & Height

These indicate the dimensions of your tiles

![](../../images/platformer/tiles_width_height.png)

### Offset

This indicates where the top left corner of the tile set should start. For example, if your sprite has blank lines at the top or along the sides, this is a great way to cut those off

![](../../images/platformer/tiles_offset.png)

### Separatation

This let's you add spacing between the tiles, but people almost always set it to 0, 0

![](../../images/platformer/tiles_separation.png)

## Make ground invisible

You may have noticed that I made the solid art a boring flat color for this one. That's because I intended to make them invisible. Click into ``oGround`` and uncheck the visible box to do this

Making the ground invisible let's me separate the functionality of the ground from the visuals. I can make the visuals whatever I want using tiles, but at the same time I still have full control over where to place the solids by adding invisible ground objects

## Placing The Tiles

To place the tiles, go into the room, and make a new tile layer. Then you can select your tile asset

Now you can just click the tiles, and place them on the tile layer 😊. Since our ground objects are now invisible, make sure you place tiles under all of the ground objects so that the player doesn't hit any surprises 😉

![](../../images/platformer/tile_placement.gif)

There's a few more tricks you should know so I'll go over those as well

### Brush Size

If you're placing a lot of the same tile, you can use the brush size to make it easier

![](../../images/platformer/tile_brush_size.gif)

### Region Selection

If you want to place a group of tiles, you can select them in the tileset and place them all at the same time. This is great if the tileset contains patterns that are larger than the alotted tile size

![](../../images/platformer/tile_region_selection.gif)

## Final Product

Here's what I came up with after applying the tiles to my room

![](../../images/platformer/tile_final.png)

TODO: insert section covering the asset layer and how you can use it for sprites that don't fit in the tile layer
