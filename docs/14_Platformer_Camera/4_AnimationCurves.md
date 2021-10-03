---
layout: default
title: Animation Curves
nav_order: 4
parent: Platformer | Camera
---

# Animation Curves

Animation curves were sort of theme in the previous section, so I figured I'd go all the way and give you a idea of how to do the transitions using actual animation curves

## Review

All our camera effects looked very different, but they each had an underlying curve which determined how the animation speed changed. This is often called easing, for example an "ease in" curve starts slow, where as an "ease out" means it ends slow

Let's take a closer look at some of the curves we already coded up. original, current, and dest represent where the value started, where it current is, and where it's going respectively. And the r parameter is a fixed value that influences how quickly the curve completes

| Feature | Code | Description | Image |
|---|---|---|
| Smooth Camera | ``current = lerp(current, dest, r)`` | It get's slow toward the end, so it's ease out | ![](../../images/platformer/smooth_camera_curve.png) |
| Camera Shake | ``current *= r;`` | Also ease out, this is actually a specific case of the previous where dest = 0 |  ![](../../images/platformer/camera_shake_curve.png) |
| Camera Transition | ``t += r; current = lerp(original, dest, t)`` | No ease, this a boring straight line / linear transition. It approaches the destination at the same rate every frame | ![](../../images/platformer/camera_transition_curve.png) |

So the camera transition was kind of boring, and on screen the linear transition looks a little abrupt. Let's fix that

Ideally I'd like to have both ease in and ease out (so start slowly, move quickly in the middle, then end slowly), but tbh I have no idea how to do that with math

Animation Curves to the rescue 🦸‍♂️

## Animation Curves
