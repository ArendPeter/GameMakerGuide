---
layout: default
title: Rooms
nav_order: 3
parent: Flappy Bird | Cameras and Rooms
---

# Rooms

Now let's add some more levels 😀

## New Room

Left click on the existing room and hit ``Ctrl + D`` to duplicate, then open up the duplicated room

To make it visually distinct, I'll just copy and paste all the pipelines so that each gap uses double pipelines

![](../../images/duplicate_pipes.gif)

> **Are you having alignment issues when pasting the new pipelines?** If so it's probably because you're zoomed in, and the paste is trying to place the new objects in view. If you scroll all the way out before pasting you should have better luck

## Enter new room from game

Next we want to enter the new room once we beat the old one. To do this we'll need to edit code in ``oBird`` again (now's a good time to mention that you can hit ``Ctrl + T`` to quickly search and access resources, including objects like ``oBird``)

Now we'll add the following code to make it go to the next room when we exit the right side of the current room

```
// oBird Step Event
//// DEATH
//// GRAVITY
//// JUMP
//// APPLY MOVEMENT
//// DEBUG RESTART
//// ANIMATE
//// NEXT ROOM
if(x > room_width){
    room_goto_next();
}
```

``room_goto_next()``: This will take as to the next room in our room order (more on updating that in the next section)

``x > room_width``: This is how we check if the bird has gone outside the room on the right side. This is doing a similar check as the **Outside Room** event, but using code we can check a specific edge

When you test it out, the bird should enter the second level as soon as it beats the first

## Updating Room Order

The ``room_goto_next()`` function as well as which room you start on all depend on the room order. To update the room order, hover the mouse to the left of one of your rooms, a button should appear. Click that button

Then you'll see a room order window, and you can drag around the rooms there to update the room order. The Home icon represents which room is first

![](../../images/update_room_order.gif)
