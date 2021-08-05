---
layout: default
title: Intro Menu
nav_order: 1
parent: Flappy Bird | Menus
---

# Intro Menu

This menu will just be a simple static menu which just asks the player to "Press space to start". Sometimes this is all you need to get the job done 😎

## New Room

Add a new room called ``rmMenu``, and let's make it look pretty. You can start w/ setting a background like we did in the other room, but then you can add asset layers to add other sprites such as the pipelines, the bird, and/or mostly importantly the title

<details data-summary="Setup the room to look pretty" markdown="1">
Here's what I came up with

![](../../images/flappy_bird/intro_menu.png)

</details>

## Press space to continue

Now let's let them press the keyboard to continue. We'll start by making a new object called ``oMenu`` (no need to set a sprite)

Here's what we want the menu object to do
 * Draw "Press space to continue" (possibly using a custom font)
 * Enters next room when space is pressed

Think you can give it a try?

<details data-summary="Can you setup oMenu object?" markdown="1">

I added a new font named ``fMenuPrompt`` **TODO** insert font and size here. Then I added the code as follows

```
// oMenu Step Event
if(keyboar_check_pressed(vk_space)){
    room_goto_next();
}

// oMenu Draw Event
draw_set_font(fMenuPrompt);
draw_set_halign(fa_center);
draw_set_valign(fa_center);
draw_set_color(c_white);
draw_text(x, y, "Press Space to continue")
```

Then this just goes to next room when you press space, and also draws the message to the screen using the new font and central alignment

How cool is it, that I can assign all these lines of code for an assignment, and you all just know how to do it. I'm so proud 😭
</details>
