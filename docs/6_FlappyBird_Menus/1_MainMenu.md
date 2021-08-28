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
if(keyboard_check_pressed(vk_space)){
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

How cool is it that I can give an assignment with so many lines of code and you all just know how to do it! I'm so proud 😭

Add it to the room and it should look like this

![](../../images/flappy_bird/intro_menu_with_text.png)

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

``position_meeting(mouse_x, mouse_y, id)``: This function asks the question *"Does this position, overlap with this object/instance"*. ``mouse_x`` and ``mouse_y`` are handy built in variables which tell us the position of the mouse in the room, and ``id`` is a handy variable which tells us the instance id of the current instance. Put it all together, and this function tells us if the mouse is hovering of the current instance 😎

``mouse_check_button_released(mb_left)``: Another new function 😮, this one is the mouse equivallent ``keyboard_check_button_released(vk_space)``. Just like with keyboard we can detect press, hold, and release for the mouse. And we can also specify a constant for which mouse button (you'll almost exclusively use ``mb_left`` or ``mb_right``). Note that we put the if statement for this condition *INSIDE* the previous if statement. This is ensures that we only trigger a button press if the mouse button is relased *AND* the mouse is hovering over the button

**Draw Event**: Hopefully the draw event is pretty straight forward. We draw the button sprite, center the text alignment, and then draw "Play" on the center of the button

<details data-summary="Why released, and not pressed or held?" markdown="1">

I usually trigger my buttons on release, but I suppose pressed would work as well (held would just be weird). The nice thing about release, is people can press the button and then drag away before release if they change their mind

Although, the current logic has some weirdness where someone could press outside the button, then drag into the button and release. More complete logic would check that they both press and release on the button, but this is fine for now

Another nice thing about release is it gives us an opportunity to make the button animate/respond to the press rather than immediately performing an action
</details>

You can delete ``oMenuPrompt`` from the room, and replace it with the new and improved ``oPlayButton``

Now when you test it out, you should be able to click the button, and enter the next game. Nice 😎

![](../../images/flappy_bird/intro_menu_button_no_animate.gif)

But that said, is it just me, or does the button seem a little dead to you ☠? Let's fix that

## Button Animation

Next we'll add some animation both when the mouse hovers over and presses the button. This will not only make the button seem more alive, but it will subltely signal to the player that this is something they can and should interact with

```
// oPlayButton Step Event
if(position_meeting(mouse_x, mouse_y, id)){
	if(mouse_check_button(mb_left)){
		sprite_index = /*insert sprite here*/;
    }else{
        sprite_index = /*insert sprite here*/;
    }

    if(mouse_check_button_released(mb_left)){
		room_goto(rm_1);
	}
}else{
	sprite_index = /*insert sprite here*/;
}
```

``sprite_index``: This built in variable let's us change the current sprite of the object. Our new code essentially extends the old one, but adds cases to account for each of the possible sprites. In this case we have ``sButtonHeld``, ``sButtonHover``, as well as the original ``sButton``

<details data-summary="See all the /*insert sprite here*/ comments? Try replacing them with the correct sprite" markdown="1">
Here's the updated code

```
// oPlayButton Step Event
if(position_meeting(mouse_x, mouse_y, id)){
	if(mouse_check_button(mb_left)){
		sprite_index = sButtonHeld;
    }else{
        sprite_index = sButtonHover;
    }

    if(mouse_check_button_released(mb_left)){
		room_goto(rm_1);
	}
}else{
	sprite_index = sButton;
}
```

Let's go through each of them

``sButtonHeld``: This line will only be trigger if the mouse is positioned over the button, and the mouse button is held down (``mouse_check_button()`` is true for every frame that the mouse button is held down), so that's when we want it to have the held down sprite. Conversely, if the mouse isn't on the button, or if the mouse isn't held down, we definitely don't want this sprite

``sButtonHover``: This signals that we're passively hovering over the button, so it's triggers when the mouse is positioned over the button, but the mouse button isn't held down (``else`` meaning the mouse button isn't held down)

``sButton``: We want this to be the default sprite, when the mouse isn't interacting with the button at all, so if the mouse isn't positioned on the button, we use this sprite. Note that we don't care whether the mouse button is pressed or not for this case

When you test this out the button should animate in response to your mouse 😁

![](../../images/flappy_bird/intro_menu_button_with_animation.gif)
</details>

> **Why sprite_index and not image_index?**: For this case I asked Brad to export the button variations as separate sprites, but I could have also asked him to export them as separate frames in the same sprite. In that case we would have used an ``image_index`` pattern similar to how we animated the player. Sometimes it's obvious that you need to use sprites, like if you're switching between multiple animations, but in this case either would have worked. I opted for multiple sprites because I hadn't taught you ``sprite_index`` yet, but normally I probably would have use the ``image_index`` pattern
