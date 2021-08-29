---
layout: default
title: Mobile Port
nav_order: 1
parent: Flappy Bird | Mobile Teaser
---

# Mobile Port

There's only one page for this section, let's go!

## Mouse Support

As a little warm up, can you update the game to make the bird respond to mouse clicks as well as mobile presses?

<details data-summary="How would you add mouse click support?" markdown="1">


```
// oBird Step Event
//// JUMP
{
    if(keyboard_check_pressed(vk_space)){
        dy = jump_dy;
    }

    if(mouse_check_button_pressed(mb_left)){
        dy = jump_dy;
    }
}
```

There's one way to do it. We already have an if statement for space, we just need another one for mouse pressed

But I'm a tad unsatisfied with this approach, because we've got some duplicated code in the ``dy = jump_dy;`` line. It's not too bad now as we mentioned in the menus chapter, duplicate code should be avoided, and as the logic grows we could have a much bigger problem

Luckily there's an easy fix 😉

```
// oBird Step Event
//// JUMP
{
    if(keyboard_check_pressed(vk_space) or mouse_check_button_pressed(mb_left)){
        dy = jump_dy;
    }
}
```

That's right! You can put both of them in the same condition by using ``or``!

More formally, ``or`` is an example of a **Boolean Logic Operator**. These operators let you combine boolean values to become a different boolean value. The ``or`` operator will output true if EITHER of the 2 booleans are true. The other popular operator is ``and`` which will only output true if BOTH booleans are true

And then to be thorough, there's also ``not``. This only applies to a single boolean and just returns the opposite. For example ``if(not position_meeting(mouse_x, mouse_y, id)){ sprite_index = sButton; }`` would have been a valid way to set the default sprite for our button (I prefer the else approach, but not is also an option )

> **What about &&, ||, and !?** oh I see this isn't your first rodeo 🤓. Many languages (C, C++, Java, etc), use &&, ||, ! instead of and, or, not. You can actually use these symbols in Game Maker as well, and many people prefer them. For me personally, I used to like &&, ||, !, because it let me save a letter on the and, but also probably because it made me feel smarter. Once I started writing python I was forced to use and, or, not, then ever since I adapted my style in Game Maker as well. Now I prefer and, or, not because it makes my code more colorful w/ game maker's sytax highlighting, and it's also more readable in general

</details>

## Mobile Demo

Surprise! In doing that assignment you just added mobile support to your game 🤯. When you run Game Maker projects on mobile, it just treats the screen touches as mouse clicks

There's still a lot of setup you'd need to do to get it to run on your phone (buy the game maker mobile module, install a bunch of software, enable developer mode on your phone, etc), but the actual code on your project won't change. Here's a little demo of me running it on my phone

![](../../images/flappy_bird/mobile_demo.gif)

If you're interested in doing the setup for yourself Game Maker has guides for both ![ios](https://help.yoyogames.com/hc/en-us/articles/115001368747-Setting-Up-For-iOS-Including-iPadOS-!) and ![android](https://help.yoyogames.com/hc/en-us/articles/115001368727-Setting-Up-For-Android) (sorry tizen and windows phone users 😢)

> **Are the mouse functions really the only way to detect taps on mobile? What if my apps need to support multi touch?** Ah great question 🤓. For multi touch you'd need to use the ``device_mouse_check_button_`` functions. In addition to accepting an ``mb_left`` argument, it also accepts the touch #. There are several edge cases to consider when using those functions, but that's out of scope for now. Maybe I'll do a mobile specific course one day
