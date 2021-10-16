---
layout: default
title: Jumper
nav_order: 1
parent: Flappy Bird | Side Projects
---

# Jumper

## Where do I start?

<details markdown="1">

Here's roughly the order I followed when implementing this project

1. Create ``sPlayer``, ``sSpike``, ``sTrampoline``, and ``sLife`` sprites
1. Create objects ``oPlayer``, ``oSpike``, and ``oTrampoline``
1. Add ``dx`` / ``dy`` and gravity logic to ``oPlayer``
1. Trigger a jump when ``oPlayer`` collides with ``oTrampoline``
1. Make the player move left/right in response to the keyboard keys
1. Make the room tall, and set the view to follow the ``oPlayer``
1. Trigger a room transition when the player goes above the room
1. Add spikes along the bottom of the room, and make them follow the ``oPlayer``
1. Restart the room when ``oPlayer`` collides with ``oSpike``
1. Create an ``oControl`` object, and make it persistent
1. Create a new room, ``rmStart``, set it to the first room, and add ``oControl``
1. Setup a lives variable in ``oControl`` and have it draw icons
1. When the play dies, update the lives and restart the room or the game accordingly

</details>

## How do I detect the keyboard left / right buttons?

<details markdown="1">

You'll use the ``keyboard_check_`` functions, just like you did when detecting space bar in flappy bird

All non-letter keyboard keys can be referenced using the ``vk_`` variables, so you can type it out and view the autocomplete whenever you're not sure what to use

In this case you should use ``vk_left`` and ``vk_right``

</details>

## Each trampoline is chaining into the next one, what's wrong here?

<details markdown="1">
If you look closely at the gif, you'll see that player can only collide with the trampolines when the player is moving downward. Otherwise dense trampolines would cause a chain reaction

To add some visual feedback, I actually gave the player 2 frames to clarify whether it's ready to land on a trampoline or not
</details>

## How do I make the lives draw on the right side?

<details markdown="1">
I switched things up to make it a tad trickier 😈

In flappy bird, I drew the lives at ``10``, ``40``, and ``70``. You could use raw numbers here as well, but I opted to reference the ``room_width`` variable, and start at ``room_width-96`` instead

Using ``room_width`` means the lives will continue to be at the correct spot even if you make the view/room wider (technically we should be referencesing the view or gui width, but we don't know how to do that yet)

Also, you may want to reverse the order of the sprites to make the 3rd life be on the right side instead of the left
</details>

## How should I handle the sides of the room?

<details markdown="1">
You have a few options

 * **Make Player Stop**: We haven't gone over this yet, but the easiest way to do add walls and leverage the ``solid`` property. When you mark an object as ``solid``, that means that other objects can't overlap when they collide with it. Emphasis on the *when they collide*! So if we create an ``oWall`` object which we put along the edges of the screen, and we don't want the player to stop when it hits it, we need to mark ``oWall`` as ``solid`` AND make a collision event in ``oPlayer``. The collision event doesn't need to have any code, but the event still needs to exist (it's weird, I know)

 * **Instant Wall Death**: You could also just make ``oWall`` kill the player, this is easier

 * **Extend Spikes**: Maybe we don't care if the player goes outside the room? But we can't have them using that strategy to get around the spikes, so at the very least we need to extend the spikes far enough outside the room to ensure that the player can't skip them

</details>

## How do I make the spikes fill the room?

<details markdown="1">
There are better ways to do this, but I actually made the spike sprites really tall (1280 px). That way they'll always extend to the bottom regardless of how high they are in the room
</details>

## I'm still hungry for more, any ideas?

<details markdown="1">

That's awesome! Here are some more avenues to consider

 * **Power Ups**: You can spawn power ups to make ``oPlayer`` jump higher, or move from side to side faster
 * **Level Menu**: Add a menu to let the player start from a different level
 * **High Score**: Keep track of the high score across multiple runs
 * **More Trampolines**: Add special trampolines that move, or do other tricks

</details>
