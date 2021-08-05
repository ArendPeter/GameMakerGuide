---
layout: default
title: Main Menu
nav_order: 1
parent: Flappy Bird | Menus
---

# Main Menu

We'll start with the main menu. This will be the player's first impression of the game, so let's make it count


## New Room

Add a new room called ``rmMenu``, and let's make it look pretty. You can start w/ setting a background like we did in the other room, but then you can add asset layers to add other sprites such as the pipelines, the bird, and/or mostly importantly the title

<details data-summary="Setup the room to look pretty" markdown="1">
Here's what I came up with

![](../../images/flappy_bird/intro_menu.png)

</details>

## "Space to Continue" Prompt

Let's start with a simple static menu which just asks the player to "Press space to start". Sometimes this is all you need to get the job done 😎

We'll start by making a new object called ``oMenuPrompt`` (no need to set a sprite)

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

## Button Logic

Next, Buttons! Should be a handy thing to know don't you think 😊

To start we'll add a play button to the main menu (possibly to replace the current prompt, but there's no harm in having both). Go ahead and make an object called ``oPlayButton``, and assign it the ``sButton`` sprite

There are many approaches to handling buttons. I feel like I discovered the hardest way first, but then I discover a new easier way every few years. Here's this years iteration

```
// oPlayButton Step Event
if(position_meeting(mouse_x, mouse_y, id)){
    if(mouse_check_button_released(mb_left)){
    	room_goto(rmLv1);
	}
}

// oPlayButton Draw Event
draw_self();
draw_set_halign(fa_center);
draw_set_valign(fa_center);
draw_text(x+sprite_width/2,y+sprite_height/2,"Play");
```

**TODO** describe code

## Button Animation

```
// step
if(position_meeting(mouse_x, mouse_y, id)){
	if(mouse_check_button(mb_left)){
		sprite_index = sButtonPressed;
  }else{
    sprite_index = sButtonHover;
  }

  if(mouse_check_button_released(mb_left)){
		room_goto(rm_1);
	}
}else{
	sprite_index = sButtonReleased;
}

// oPlay
draw_self();
draw_set_halign(fa_center);
draw_set_valign(fa_center);
draw_text(x+sprite_width/2,y+sprite_height/2,"Play");
```
