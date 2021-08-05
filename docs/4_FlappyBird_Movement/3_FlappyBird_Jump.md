---
layout: default
title: Jump
nav_order: 3
parent: Flappy Bird | Movement
---

# Jump

Next we'll make the birdie jump whenever we hit spacebar, and we'll learn to do this without needing keyboard events

## Keyboard events in code

Here's the jumping code

```
// oBird Create Event
...
jump_dy = -10;

// oBird Step Event

//// DEATH
...

//// GRAVITY
...

//// JUMP
{
    if(keyboard_check_pressed(vk_space)){
        dy = jump_dy;
    }
}

//// APPLY MOVEMENT
...
```

If you test the code, you should see the bird jump when you press space

``jump_dy``: Jumps work by resetting the dy to go in a upward direction, and then letting gravity pull the dy back down again. ``jump_dy`` is the value we'll set dy to when jumping. In addition to making the code more readable, using a ``jump_dy`` variable also makes it easier to tweak that value (if you're working in a team, it's a lot easier to tell people to tweak a variable in the create event, rather than find the number in your step event)

``keyboard_check_pressed()``: This works similarly to ``place_meeting()`` from the last section, we ask it if a particular key is being pressed, and then if it is's it'll return a boolean. Btw, this has the same pressed, down, released variations as the events had, they're just ``keyboard_check_pressed()``, ``keyboard_check()`` and ``keyboard_check_released()`` respectively

``vk_space``: This is a constant, similar to the color constants, there's a whole set of key constants. This way you don't have to memorize numbers 😉. You can type ``vk_`` and then hit ``ctrl + space`` for the whole set of keyboard constants

![](../../assets/images/pong/vk_autocomplete.png)


**Inlined Code**: The ``keyboard_check_pressed()`` pattern is similarly to the one I used for ``place_meeting()`` except I **inlined** ``keyboard_check_pressed()`` into the if statement, whereas I **extracted** ``place_meeting()`` out into the ``colliding_with_pipe`` variable. Which one's better? Think on it and get back to me

<details data-summary="Is it better to inline, or extract conditions for if statements?" markdown="1">

I could have actually used either in both cases (the if statment receives a boolean for the condition either way), but there are still some pros and cons

Usually I prefer inlining since it looks cleaner and there's less code, but sometimes I still extract conditions out to variables because it let's me essentially use the assign a name to the condition (kind of like using the variable name as comment). This can make the code more readable

For this case I extracted ``place_meeting()`` out to a variable, since I thought it would be easier to explain that way. Then I felt like you were ready for inlining as an added challenge. But since ``place_meeting()`` and ``keyboard_check_pressed()`` are already fairly readable on their own, I probably would have just inlined in both cases if I was writing this for my own project

But this is also personal preference, as you write more code, you'll get a sense for your preferences on these things and you can follow your heart 💖

</details>

**Code Order**: Note that I'm being very careful with my code order here. Specifically I chose ``GRAVITY`` the, ``JUMP`` then ``APPLY MOVEMENT``. Most importantly you should make sure ``APPLY MOVEMENT`` is always last. Otherwise, if ``JUMP`` was at the end, jumps would update the ``dy``, but then there'll be a frame delay before we see that ``dy`` applied. I also like have ``JUMP`` after ``GRAVITY``, because otherwise I'd jump, and then ``GRAVITY`` would immediatly weaken the jump. Meaning my ``jump_dy`` wouldn't necessarily be accurate. In this case the changes wouldn't be super noticable, but it's still good to keep this in mind
