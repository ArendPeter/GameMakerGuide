---
layout: default
title: Juggler
nav_order: 1
parent: Pong | Side Projects
---

# Juggler

Here's some FAQ to help you along with the Juggler project, I'll

TODO: post how they can ask more questions

## Where do I start?

<details markdown="1">

Starting with a blank project can be kind of intimidating. Here's one way you can break the project into smaller tasks

1. Create ``sWall``, ``sBall`` and ``sPaddle`` sprites
1. Create corresponding ``oWall``, ``oBall``, and ``oPaddle`` objects
1. Make ``oPaddle`` move left/right
1. Make ``oBall`` fall
1. Add a collision between ``oPaddle`` and ``oBall`` to handle bounce (mine has aiming, but you can start with a basic collision)
1. Add a collision between ``oBall`` and ``oWall`` as well
1. Use the outside room event to make ``oBall`` reset
1. Draw the points (I handled it from the ``oPaddle``, but you can do any object)
1. Increase points whenever ``oBall`` collides w/ ``oPaddle``
1. Reset points when ``oBall`` goes outside the room
1. Add a custom font for the points

</details>

## How do I implement gravity?

<details markdown="1">

Use the built in [gravity](https://manual.yoyogames.com/GameMaker_Language/GML_Reference/Asset_Management/Instances/Instance_Variables/gravity.htm) variable

This defines how quickly the vspeed increases (acellerates), and you setting the variable in our create event should be enough

```
// oBall Create Event
gravity = .5;
```
</details>

## The ball bounces get shorter everytime, how do I make it consistent?

<details markdown="1">

Yeah, I hit this issue too and it caught me off guard

I'm actually not sure why this happens? My best guess is a [precision error](https://www.youtube.com/watch?v=PZRI1IfStY0)

Anyway, it happened to me because I was only setting the ``direction`` in the ``oBall`` and ``oPaddle`` collision event. If I set ``speed`` and ``direction`` that keeps the bounces more consistent

</details>


## My ball keeps going through walls, how do I fix it?

<details markdown="1">

There's a couple of reasons why this could happen

1. For starters, make sure your collision event is correct. There should be a ``oWall`` collision event, in the ``oBall`` object
1. Assuming you implement the collision event as ``hspeed = -hspeed``, it's possible the collision event is triggering multiple times and the ball is getting stuck. Making ``oWall`` solid can help with this
1. If solid isn't enough to fix it, then we need to add logic to guarantee that the hspeed comes out in the right direction. You can use an if statement together with ``abs()`` to acheive this. So if the ball is all the left side of the room, we want ``hspeed`` to become positive, and if it's all the right side we want ``hspeed`` to be negative

</details>
