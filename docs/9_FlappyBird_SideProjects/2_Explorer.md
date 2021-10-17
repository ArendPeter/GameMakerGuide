---
layout: default
title: Explorer
nav_order: 2
parent: Flappy Bird | Side Projects
---

# Explorer

## Where do I start?

<details markdown="1">
I'm going to break this one down a little more hierarchically

<details data-summary="Implement player movement" markdown="1">
1. Make sprites ``sPlayer``, ``sGrid``, ``sFlag`` and ``sWall``
1. Make objects ``oPlayer``, ``oFlag``, and ``oWall``
1. Make player move 1 grid space at a time in response to key presses
1. Center view on player
1. Trigger next room when ``oPlayer`` collides with ``oFlag``
1. Make ``oPlayer`` stop when collides with ``oWall``
</details>

<details data-summary="Implement coins" markdown="1">
1. Make sprites ``sCoin``, and object ``oCoin``
1. Increase coin count when ``oPlayer`` and ``oCoin`` collide (and also destroy the coin)
1. Draw the coin count to the GUI
</details>

<details data-summary="Implement lock & key" markdown="1">
1. Make sprites ``sKey1``, ``sKey1Icon``, ``sKey2``, ``sKey2Icon``, ``sLock1``, and ``sLock2``
1. Make objects ``oKey1``, ``oKey2``, ``oLock1``, and ``oLock2``
1. Set booleans whenever ``oPlayer`` collides with any of the keys
1. Make the locks solid, unless the player has the correct key
1. Destroy locks when player collides with them (if we use the solid feature, this should only be possible when the player has the correct key)
1. Draw the key icons for the player
</details>

<details data-summary="Implement terrain & shoes" markdown="1">
1. Make sprites ``sFlippers``, ``sWater``, ``sSkates``, ``sIce``
1. Make objects ``oFlippers``, ``oWater``, ``oSkates``, ``oIce``
1. The reset is essentially the same as with lock & key, except the terrain/shoes don't get destroyed when they're used
</details>

</details>

## How do I keep the player above the terrain?

<details markdown="1">
The order that objects are drawn is determined by the room layers. In my case I put the player on it's own layer above all the other objects to make sure it was never behind anything

Sometimes you get lucky, and it shows up above everything anyway, but using layers is probably a good habit anyway
</details>

## How do I make the walls stop the player?

<details markdown="1">

This is actually kind of tricky. I haven't gone over it in the course, but I did touch on it in the Jumper FAQ. I'll rehash it here

The easiest way to do add walls and leverage the ``solid`` property. When you mark an object as ``solid``, that means that other objects can't overlap when they collide with it. Emphasis on the *when they collide*! So if we create an ``oWall`` object which we put along the edges of the screen, and we don't want the player to stop when it hits it, we need to mark ``oWall`` as ``solid`` AND make a collision event in ``oPlayer``. The collision event doesn't need to have any code, but the event still needs to exist (it's weird, I know)

I'm not a big fan of this method, it feels really weird. We'll go over better approaches for the next game
</details>

## How to I make ``sGrid`` tile on the background?

<details markdown="1">
I just set ``sGrid`` as the background sprite in the background layer

If you do that and check the boxes for "Horizontal Tile" as well as "Vertical Tile" you should be golden
</details>

## Do I need ``oControl`` in this one?

<details markdown="1">
In Flappy Bird we used ``oControl`` to manage variables that were persistent across the levels. In this case I want the coins to be persistent, but not the item flags (players will need to rediscover the items for every puzzle)

So I do recommend using ``oControl`` for the coins, but not for the item flags

As a result I ended up with Draw GUI events in both ``oControl`` and ``oPlayer`` so I just needed to be careful that the coin text and the flag icons didn't overlap
</details>

## Where can I learn move about designing levels like these?

<details markdown="1">

This idea was inspired by Chip's Challenge (it was nostalgic for me, not sure how many people share this). I featured some of the easier mechanics here, but there's a lot more to choose from it you want a challenge 😉😉

<iframe width="560" height="315" src="https://www.youtube.com/embed/pcdMh1M7QLI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Also, in Mark Brown's videos analyzing the Zelda games, he came up with a diagraming method to communicate the lock/key flow in Zelda. You can actually break our levels into those same diagrams, so it could be a good thing to try

<iframe width="560" height="315" src="https://www.youtube.com/embed/fqKGl6exyyY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

</details>



popper ideas
