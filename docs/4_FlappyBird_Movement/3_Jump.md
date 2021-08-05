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
//// GRAVITY
//// JUMP
{
    if(keyboard_check_pressed(vk_space)){
        dy = jump_dy;
    }
}

//// APPLY MOVEMENT
```

If you test the code, you should see the bird jump when you press space

``jump_dy``: Jumps work by resetting the dy to go in a upward direction, and then letting gravity pull the dy back down again. ``jump_dy`` is the value we'll set dy to when jumping. In addition to making the code more readable, using a ``jump_dy`` variable also makes it easier to tweak that value (if you're working in a team, it's a lot easier to tell people to tweak a variable in the create event, rather than find the number in your step event)

``keyboard_check_pressed()``: This works similarly to ``place_meeting()`` from the last section, we ask it if a particular key is being pressed, and then if it is's it'll return a boolean. Btw, this has the same pressed, down, released variations as the events had, they're just ``keyboard_check_pressed()``, ``keyboard_check()`` and ``keyboard_check_released()`` respectively

``vk_space``: This is a constant, similar to the color constants, there's a whole set of key constants. This way you don't have to memorize numbers 😉. You can type ``vk_`` and then hit ``ctrl + space`` for the whole set of keyboard constants

![](../../images/pong/vk_autocomplete.png)


**Inlined Code**: The ``keyboard_check_pressed()`` pattern is similarly to the one I used for ``place_meeting()`` except I **inlined** ``keyboard_check_pressed()`` into the if statement, whereas I **extracted** ``place_meeting()`` out into the ``colliding_with_pipe`` variable. Which one's better? Think on it and get back to me

<details data-summary="Is it better to inline, or extract conditions for if statements?" markdown="1">

I could have actually used either in both cases (the if statment receives a boolean for the condition either way), but there are still some pros and cons

Usually I prefer inlining since it looks cleaner and there's less code, but sometimes I still extract conditions out to variables because it let's me essentially use the assign a name to the condition (kind of like using the variable name as comment). This can make the code more readable

For this case I extracted ``place_meeting()`` out to a variable, since I thought it would be easier to explain that way. Then I felt like you were ready for inlining as an added challenge. But since ``place_meeting()`` and ``keyboard_check_pressed()`` are already fairly readable on their own, I probably would have just inlined in both cases if I was writing this for my own project

But this is also personal preference, as you write more code, you'll get a sense for your preferences on these things and you can follow your heart 💖

</details>

**Code Order**: Note that I'm being very careful with my code order here. Specifically I chose ``GRAVITY`` the, ``JUMP`` then ``APPLY MOVEMENT``. Most importantly you should make sure ``APPLY MOVEMENT`` is always last. Otherwise, if ``JUMP`` was at the end, jumps would update the ``dy``, but then there'll be a frame delay before we see that ``dy`` applied. I also like have ``JUMP`` after ``GRAVITY``, because otherwise I'd jump, and then ``GRAVITY`` would immediatly weaken the jump. Meaning my ``jump_dy`` wouldn't necessarily be accurate. In this case the changes wouldn't be super noticable, but it's still good to keep this in mind

## Add restart button

Now that we know how to work with the keyboard, why don't you make the game restart when you press **R**. Always nice to have 😊 (this is a bit of a trick question since there's no such thing as ``vk_R``, read up on [ord](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Strings/ord.htm) to help get you started)

<details data-summary="How do you make R trigger a restart?" markdown="1">

```
// oBird Step Event

//// DEATH
//// GRAVITY
//// JUMP
//// APPLY MOVEMENT
//// DEBUG RESTART
if(keyboard_check_pressed(ord("R"))){
    game_restart();
}
```

Pretty similar to jump, you just need to do ``ord("R")`` instead of ``vk_space``, but be sure that it's capitol R, that's a frequent gotcha

</details>

## Animate Jump

Remember how the bird was animated? Let's implement that now

The bird has 2 frames, one for going up (frame 0), and on for going down (frame 1). Here's the code to make it set the frame accordingly

```
// oBird Step Event

//// DEATH
//// GRAVITY
//// JUMP
//// APPLY MOVEMENT
//// DEBUG RESTART
//// ANIMATE
{
    if(dy < 0){
        image_index = 0;
    }else{
        image_index = 1;
    }
}
```

``image_index``: We didn't use this variable in our previous animation experience, but this represents the current frame of our animation. This if statement will just check if the bird is moving up, and set the animation frame accordingly

This is a very small tweak but it adds so much to the look of the game 🥰

![](../../images/flappy_bird/bird_animated.gif)

## show_debug_message()

Let's say we wanted to get more insight into the animation logic, just to make sure it's working as we expect

For the moment it's tricky, because the game is moving so fast. One way around that is to use ``show_debug_message()``, this lets you print info to the console so that we can review it later. Let's add this to our animation logic

```
// oBird Step Event
//// DEATH
//// GRAVITY
//// JUMP
//// APPLY MOVEMENT
//// DEBUG RESTART
//// ANIMATE
...
show_debug_message(dy+" "+image_index);
```

Here we're printing our dy and our image_index to the console every frame, and we're adding a space in between so that we can tell the numbers apart

But when we run this you'll see an error 😱

```
___________________________________________
############################################################################################
ERROR in
action number 1
of  Step Event0
for object oBird:

unable to convert string " " to float
 at gml_Object_oBird_Step_0 (line 29) - show_debug_message(dy + " " + image_index);
############################################################################################
gml_Object_oBird_Step_0 (line 29)
```

No worries, there's an explanation for this, it's just annoying

## Concatenation Logic

So we've talked about datatypes before, there have essentially been 3 that we've worked with **string**, **real** and **boolean**, and Game Maker sees these types as fundamentally different, (or rather strings and reals are fundamentally different booleans are sort of a *fake* datatype since game maker just treats them like reals). So when if we try doing ``1 + " "``, we can intuit that the result should be ``"1 "``, but Game Maker can't do it. To Game Maker it's like trying to add an apple and an orange

To fix this, we just convert the numbers into strings to make the type match

```
// oBird Step Event
//// DEATH
//// GRAVITY
//// JUMP
//// APPLY MOVEMENT
//// DEBUG RESTART
//// ANIMATE
...
show_debug_message(string(dy)+" "+string(image_index));
```

When you run this, you should see ``dy`` and ``image_index`` values printed to the console, and we should see that ``image_index`` is ``0`` for negative ``dy`` values, and ``1`` otherwise. Yay! 🎉

``string()``: This takes a value (usually a real), and returns a string version of that value. So ``string(1) + " "``, would become ``"1" + " "``, and game maker can work with that to become ``"1 "``

**concatentaion**: By the way, the process of connecting strings together like this is called **concatenation**, and these sorts of conversion issues are pretty common when you're trying to do string **concatenation**


## print()

So, if you're anything like me, you might have come out of that section a tad annoyed. The ``show_debug_message()`` function name is tedious to type, and the ``string(dy) + " " + string(image_index)`` is even more tedious. Overall, if it takes that much effort to print stuff out, it takes a lot more will power to do it, even if it can give me helpful information

Luckily, Game Maker let's us add our own custom functions, so many people (me included), wrote our own custom function that was easier to use

Creating your own custom functions is out of the scope for this course, but this particular function is so helpful, that I'll just walk you through how to copy mine

1. First create a script (right click on the script folder, and select ``Create > Script``)
2. Then copy my function from [here](https://gist.githubusercontent.com/ArendPeter/bf837f5fd32284944f7e898e384f11d2/raw/9e1a9f4c22e2abe8b3ef8bd9b6d097ca501620f1/print.gml) (open the link, ``ctrl + A`` to select all, ``ctrl + C`` to copy)
3. Paste the code into the script

Nice! Now we can call ``print()`` the same way we would any other function, let's update our code

**BEFORE**

```
show_debug_message(string(dy)+" "+string(image_index));
```

**AFTER**

```
print(dy, image_index);
```

There isn't that new version much better? I even automatically put the ``" "`` in between since I almost always want it anyway

``print()``: This takes an arbitrary number of parameters of any type. It'll combine the parameters, and print them using ``show_debug_message()``
