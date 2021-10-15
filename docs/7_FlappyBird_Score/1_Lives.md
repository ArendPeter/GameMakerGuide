---
layout: default
title: Lives
nav_order: 1
parent: Flappy Bird | Score
---

# Lives

Let's add some lives ♥♥♥

## Persistent Objects

So we'll need a variable ``num_lives`` keeping track of how many lives we have right? We know how to make variables, but the bigger question is where do we put this variable? The create events of each object are all re-triggered everytime the room restarts, so if we started our lives off at 3, then they'd be reset every time the room restarted .... hmmm

The answer, **Persistent Objects**, these are objects that we make once, and then they'll persistent across room transitions. We tend to use these whenever we have information that's needed across rooms (such as lives). Go ahead and make an object called ``oControl``, and then check persistent box on the object (no need to give it a sprite)

![](../../images/flappy_bird/oControl_persistent.png)

Then let's create a new room, called ``rmStart``. Set this to be the first room, and then add ``oControl`` (this room will be empty except for ``oControl``). Now we can add the following code

```
// oControl Create Event

num_lives = 3;
room_goto(rmMainMenu);
```

Now we've got our ``num_lives`` variable defined in a persistent place, and then we'll immediately got to ``rmMainMenu`` before the player even notices that we were in ``rmStart``

> **Why do we need a separate room for oControl?** Good question! We want oControl to be accessible throughout the game, so it's important to add it to the first room. But ``rmMainMenu`` wouldn't be a good option, because it's possible to go back to the main menu from the level select room. This could result in a new ``oControl`` instance everytime we enter ``rmMainMenu``, and things could get confusing. So instead I hacked in ``rmStart`` to have a room that's only spawned at the very beginning of the game

## Updating the lives

We already have logic for making the bird die, but let's use lives to make it more interesting

```
// oBird Step Event

...

//// DEATH
{
    colliding_with_pipe = place_meeting(x, y, oPipe);
    if(colliding_with_pipe){
        if(oControl.num_lives == 0){
            oControl.num_lives = 3;
            room_goto(rmMainMenu);
		}else{
			oControl.num_lives--; // oControl.num_lives -= 1;
			room_restart();
        }
    }
}

...

```

So if we die and we're out of lives, we want the game to restart, otherwise, we reduce the number of lives by 1, and also restart the room

``if(oControl.num_lives == 0){``: Here's how we check if we're out of lives. Remeber to use the ``oControl.`` because we're specifically refering to the ``num_lives`` variable that we just defined in ``oControl``

``room_goto(rmMainMenu);``: We're going back to the main menu to reset, but since oControl is persistent, we need to make sure to reset the lives. Otherwise we'd start the next level with 0 lives and then we'd immediately be pushed back to the menu again. (NOTE: this is similar to ``game_restat()``, but I specifically opted against it because this approach will make high scores a little easier. I'll talk about it more later)

``oControl.num_lives--;``: Here we reduce our lives by one. This is equivalent to ``oControl.num_lives -= 1;``, but it turns out that adding and subtracting by 1 is super common, so game maker gave us an quicker way to do it (``oControl.num_lives++;`` would be the addition equivalent)

``room_restart();``: Should be pretty straight forward, it just restarts the room, I just wanted to call it out since this was our first time using it (``room_goto(room);`` is an equivalent, the built in ``room`` variable just refers to the current room)

Cool, now if you test it out, the logic should work, but we can't see our current lives. Let's fix that

## Drawing the lives

Now let's draw the lives! The idea I'm going with here is to draw a number of hearts equal to how ever many lives we have. If I think about it, here's how I want the hearts to look for each number of lives

* 3 lives: ♥♥♥
* 2 lives: ♥♥
* 1 lives: ♥
* 0 lives:

So we could say that the far right heart, is only drawn in the 3 lives scenario. Then the middle heart is only drawn for 2 or more, then the final one would be one or more. I can apply if statements for each one to achieve this effect

**Keyboard Shortcuts**: This is a good opportunity to practice your keyboard short cuts from earlier. After writing the first if statement, try copy pasting it, and then use your navigation shortcuts to edit the specific parts that need editing

```
// oControl Draw GUI Event
//// LIVES
if(num_lives >= 1){
    draw_sprite(sLife, 0, 10, 10);
}
if(num_lives >= 2){
    draw_sprite(sLife, 0, 40, 10);
}
if(num_lives >= 3){
    draw_sprite(sLife, 0, 70, 10);
}
```

There hopefully that's pretty self explanatory after explaining our strategy 😊. When you test it, you should get the lives visuals as well as the logic

![](../../images/flappy_bird/lives_icons.gif)

**Why the Draw GUI event instead of the Draw event?**: Great question! The positions in the draw event refer to positions in the room, where as Draw GUI let's us refer to positions on the screen (which is where you'd be drawing your GUI, or Graphical User Interface). To understand this better, you can try changing the event back to Draw (make sure not to forget to add ``draw_self()``, it's required for Draw but not Draw GUI). You'll notice that the lives are drawn in the top left of the room, but once the bird flies away from that corner, the lives will leave the view. We use Draw GUI so that it's always in the top left corner of the screen

![](../../images/flappy_bird/lives_icons_draw_event.gif)
