---
layout: default
title: Death
nav_order: 2
parent: Flappy Bird | Movement
---

# Death

Death is simple, if the bird hits something, restart the game. Previously we would have used a collision event, but as a continuation of the relearning process I'm going to show you how to handle collisions in code within the step event

## Collision

Add an object name ``oPipe`` and assign the corresponding sprite. Then we can add the following code to the bird step event

```
// oBird Step Event

//// DEATH
{
    colliding_with_pipe = place_meeting(x, y, oPipe);
    if(colliding_with_pipe){
        game_restart();
    }
}

//// GRAVITY
...

//// APPLY MOVEMENT
...
```

``place_meeting``: By calling this function with values ``x``, ``y``, and ``oPipe``, we're asking the question "If I'm at position ``x``, ``y`` will I be colliding with an instance of ``oPipe``?", and then it returns a boolean depending on the answer.

``colliding_with_pipe``: We then store that boolean in ``colliding_with_pipe`` to represent whether or not we're currently colliding with a pipe. And since it's a boolean, we can use that as our condition of the if statement. From there we'll restart the game if the condition is true

## Room placement shortcuts

Now we're going to place the pipelines into the rooms, but there's some tricks we can use to make this easier on ourselves

![](../../assets/images/pong/pipe_mirror.gif)

**Mirroring**: We want both upside down sprites, as well as normal ones. Instead of using a new sprite we can mirror it by resizing the pipe. This is the same as giving it a negative ``image_yscale``

![](../../assets/images/pong/pipe_duplication.gif)

**Duplication**: We can use some keyboard shortcuts to make duplciation easier, in particular here are the ones I used

``ctrl + left click``: Left clicking usually selects an individual instance, but if you're holding control, then you can select multiple
``shift + left drag``: If you want to select a bunch over a region, then you can use this to create a rectangle and select all in that region
``ctrl + c``: As usual, you can use this to copy whatever you have selected
``ctrl + v``: Then you can use this to paste

I use these shortcuts to select pairs of pairs and duplicate them

![](../../assets/images/pong/pipe_floor.gif)

**Adding a floor**: I also want the bird to die when it's outside the room. I could use the outside room event for this, but I already have the logic setup for the pipes, so may as well have a big pipe placed below the room

Then when we test the game, we should see it restart whenever the bird hits a wall

![](../../assets/images/pong/bird_death.gif)
