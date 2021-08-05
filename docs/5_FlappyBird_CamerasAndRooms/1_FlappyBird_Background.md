---
layout: default
title: Background
nav_order: 1
parent: Flappy Bird | Cameras and Rooms
---

# Background

Let's add a background as a minor update before getting into the cameras. This should help make the panning cameras more clear (also having a pretty game helps make bugs less frustrating 😉)

## Updating background layer in the room

Our room already has a background layer, but it's set to a solid color by default

Go ahead and click on the background layer, then under the layer properties you can set ``sBackground`` as a sprite

![](../../images/flappy_bird/setting_background.gif)

Also I went ahead and checked the horizontal tile box. This background is setup to line up seamlessly when you place multiple next to each other, so this will allow us to make our room larger later
