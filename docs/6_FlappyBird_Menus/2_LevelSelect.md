---
layout: default
title: Level Select
nav_order: 2
parent: Flappy Bird | Menus
---

# Level Select

Now that we know how to make one button, let's make a bunch more and form a level select menu 🤩

## Entering Level Select Menu

Next let's make an additional room for level select, feel free to duplicate ``rmMenu`` rename it to ``rmLevelSelect``, and then make some room tweaks so that we can tell them apart

Now we need to make an additional button for entering the level select menu. It's going to be very similar to ``oPlayButton``, so start by right clicking on ``oPlayButton`` and selecting **Duplicate Object**. Then we'll make some updates to the code

```
// oPlayButton Step Event
...
room_goto(rmLevelSelect);
...

// oPlayButton Draw Event
...
draw_text(x+sprite_width/2,y+sprite_height/2,"Level Select");
...
```

The code will be identical, except we want to make it go a different room, and have different text on the button. So just update those lines

**Cursor Navigation Shortcuts**: Most of your time writing code will actually involve scrolling through existing code and making tweaks. Here are some keyboard shortcuts that can help you with that
 * ``arrow keys``: I'm sure you already know that you can use the arrow keys to navigate through your code
 * ``ctrl + arrow keys``: If you hold ctrl while navigating you can move the cursor much faster
 * ``shift (+ ctrl) + arrow keys``: On top of that you can add ``shift`` to the mix to select while you're navigating (with or without ``ctrl``). Once you have text selected, you don't necessarily need to copy it. You can also just start typing if you want to overwrite it

So for this case I navigated to and selected ``rmLv1`` and then I typed ``rmLevelSelect``. Much faster than the mouse technique once you get used to it. Maybe you can try changing to code back and forth a few times to practice, ``rmLevelSelect``/``rmLv1`` and ``"Play"``/``Level Select`` respectively

Now add the new button to the room, and this one should take you to the level select room

![](../../images/flappy_bird/enter_level_select.gif)

## Refactor oButton

Now we could use the same approach to make more buttons to correspond to each of our levels, but this actually creates a problem with for ourselves

**Code Duplication vs Code Reuse**: We're about to add a bunch of buttons, but if we keep using the current approach we're going to have the same code duplicated across many objects. This will be fine until a day comes where we want to update that code (for example, what if we wanted to change the font color on the buttons?), and that would be super tedious (imagine if we had a dozen buttons 😲) And since we're only human, we'll probably forget to update a few of them, and we'll end up in a constant cycle of fixing the buttons that are out of sync with the latest code change. To avoid this, we should always aim to maximize **Code Reuse** (it's also often called **Code Sharing**, I'll use these interchangably)

There are many approaches for code sharing. In this case we're going to create a single ``oButton`` object, and reuse that for each of our buttons. So go ahead and duplciate ``oPlayButton`` again, and name the new object ``oButton``, then let's start by listing the current manual changes that we're making when duplicating our buttons

* **target_room**: we update which room the button takes us to
* **text**: we update the text that's drawn on top of the button

Our current approach with these variables is actually another example of **Hard Coding**. Just like with the magic numbers conversation, we're placing the values directly in the code rather than in variables. Let's fix that now, instead of replacing those values with new hard coded values, let's replace them with variables

```
// oPlayButton Step Event
...
room_goto(target_room);
...

// oPlayButton Draw Event
...
draw_text(x+sprite_width/2,y+sprite_height/2,text);
...
```

Next we need to initialize them. We could just do this in the Create event, but instead I'm going to do it in the variables definitions of the object (I'll use the same values as ``oPlayButton`` for now, but we'll update this later)

![](../../images/flappy_bird/oButton_variables.png)

The coolest thing about variable definitions is we can set their values within the room. So we can actually remove ``oPlayButton``, and ``oLevelSelect`` from the main menu, and replace them with ``oButton`` instances

![](../../images/flappy_bird/main_menu_with_oButton.png)

Pretty cool right 😉, now we can continue adding an arbitrary number of buttons without

## Level Buttons

As usually, if you do a good job refactoring, then the adding new features will become super easy. How about you try using the concept from the previous section to add the following buttons to level select

 * **Level 1**
 * **Level 2**
 * **Back**

<details data-summary="How would you add the new buttons to ``rmLevelSelect``?" markdown="1">

No new code 😁, here's how I set it up in room

![](../../images/flappy_bird/level_select_with_oButton.png)

</details>

When we test it out, you should be able to navigate with the buttons to your hearts content

![](../../images/flappy_bird/oButton_gameplay.gif)
