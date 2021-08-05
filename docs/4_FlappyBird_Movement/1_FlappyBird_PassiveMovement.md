---
layout: default
title: Falling
nav_order: 1
parent: Flappy Bird | Movement
---

# Falling

First I'll go over the passive portion of the flappy bird movement. So that includes scrolling to the right and falling

## Setup Resources

As I mentioned at the top of the chapter, art isn't something I'll be teaching so we'll be using art made by Brad (the Star Garden Games artist) for the remainder of the couse

Let's just import all our sprites in one go. Navigate to the flappy bird images included with the course, and drag them into game maker to create the sprites. You should end up with the following sprites

* ``sBackground``
* ``sBird``
* ``sButton``
* ``sButtonHover``
* ``sButtonPressed``
* ``sPipe``
* ``sTitle``

Then we'll go ahead and make an object for the bird. Now that we're on our second game, I'm going to be recommending keyboard shortcuts wherever possible. So click the object folder and press **Alt + O** to create the ``oBird`` object

## Moving Right

Using the techniques from the last chapters, we'd probably just set ``hspeed = 4;``, but I want to introduce you to a new technique. Instead of using the built in ``hspeed`` / ``vspeed``, we'll make our own variables ``dx`` / ``dy``

```
// oBird Create Event
dx = 2;
dy = 0;

// oBird Step Event

//// APPLY MOVEMENT
{
    x += dx;
    y += dy;
}
```

If you throw ``oBird`` into a room and test it, you'll see that it moves to the right (similar to the ball when working on the Pong game)

![](../../assets/images/pong/bird_moving_right.gif)

The only difference with the ``dx`` / ``dy`` technique, is we need to manually apply the speed to our ``x`` / ``y`` every step. ``dx`` is actually short for **delta x** which is the math term for **change in x**, so that's why it represents how much x changes every step

``hspeed`` / ``vspeed`` were doing this too, but it was just done automatically by game maker. The nice thing about ``dx`` / ``dy`` is we can control exactly when this change is applied, which will be nice as our game gets more complicated

For example, adding a pause feature would be easier with ``dx`` / ``dy``. With ``hspeed`` / ``vspeed`` we'd need to set both of them to 0, but then also store the old version so that we can reset once the game is unpaused. For ``dx`` / ``dy`` we can just add an if statement around the ``APPLY MOVEMENT`` section and we can leave the values unchanged

**What does the { } do?**: I'm using these to help organize the code. In the code editor you'll notice that you expand and reduce the code within that block. Sometimes I like doing this to help keep my code organized

> **PRO TIP, select + tab**: Sometimes I add the brackets after I've written the code. In this case it can be tedius to insert a tab before each line. As a quick way around this, most editors will let you select code, and hit ``tab`` to tab everything out in one go. Similarly you can hit ``shift + tab`` to go back

![](../../assets/images/pong/code_expansion_shift_tab.gif)

## Gravity

Game Maker also has a built in solution for ``gravity``, but we'll avoid it for the same reason that we're avoiding ``hspeed`` and ``vspeed``.

```
// oBird Create Event
...
grav = .5;

// oBird Step Event

//// GRAVITY
{
    dy += grav;
}

//// APPLY MOVEMENT
...
```

In the interest of reducing **Magic Numbers** I'm creating a separate variable for ``grav`` rather than having ``.5`` hard coded into the step event. This will also make it easier to tweak the gravity force in case we want to adjust the feel of the game later

I could have technically called ``grav`` ``ddy`` instead. Since we're just adding it to ``dy`` on each step "change in the change in y" would be an apt name. This fits nicely with the proper physics definitions. Position (``x`` / ``y``), represents where you're located, Velocity (``dx`` / ``dy``) represents how fast your position is changing, and then Acceleration (``ddx`` / ``ddy``) represents how fast your velocity is changing. And that's all gravity is, "Acceleration". As your falling, gravity will make your velocity faster. So the longer you fall, the faster you go

All that said, I usually don't call things ``ddy``, since there are usually multiple things affecting the speed and I want to make sure I can tell them apart

When you test the game, we should see the bird "falling" since gravity is increasing the downward velocity over time

![](../../assets/images/pong/bird_falling.gif)

What do you think would happen if we decreased the grav variable? Think about it, then test it and get back to me

<details data-summary="What would happen if you reduced the grav variable?" markdown="1">

Decreasing the grav variable, will make the bird seem more "floaty". As we develop the game, you can keep adjusting the variable to make it feel just right

This is just how gravity works in real life. On Earth gravity is **9.8 meter/second^2**, but on the moon it's **1.6 meter/second^2**, and that's why astronauts are so floaty 😉

But on the flip side, if you went some where larger than Earth, like Neptune, gravity would actually be higher. Neptune's gravity is **11.1 meter/second^2** so I guess you'd feel heavier there? Although heavy isn't technically correct because gravity accelerates us at the same rate regardless of how heavy we are. (For example ![feathers and hammers drop at the same rate when you remove air resistance](https://www.youtube.com/watch?v=KDp1tiUsZw8))

</details>
