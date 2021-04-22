---
layout: default
title: GML Basics
nav_order: 5
parent: Pong | Your First Game
---

# GML Basics

The the previous section we just had a BIG first, we ran our game for the first time :D. This section will be a rapid fire of more BIG firsts. Here's the list

 * We'll change instance positions with code 😎
 * We'll use keyboard input to make the game interactive 😲
   * and we add local co-op while we're at it 😵
 * We'll make instance interact with each other 🤯

That's all on this page!! Let's do it!

## x / y axis

Let's look back at the instance properties in the room editor. Earlier we modified the scale x property, but there's much more where that came from and we'll quickly become familiar with all of them throughout the course. The most important properties here are the x and y, so dig into those next. You were probabaly wondering what the X by Scale X meant. x and y are pretty much just short hands for horizontal and vertical respectively. So why didn't they call it horizontal scale, and vertical scale instead? Well we refer to x and y so often that they it wouldn't quickly become tedious to always need to say horizontal or vertical.

> TODO: png of instance properties

So with that said the x and y variables represent where each instance is positioned in the room horizontally and vertically. If you drag one of your instances to the right you can see that the x increases. Similarly if you drag the instance down you see that y increases. I can also set the x and y directly in the instance properties. If I set it to x = 0, and y = 0, where do you think the instance will move to? (I'm hiding the rest of the section for you to think about it 😉 )

> TODO: gif moving instance left/right


<details>
<summary> <b>Where in the room is x = 0, y = 0?</b> click to see </summary>

<p>
If you said, top left corner, you would be right! Since moving right and down made both variables bigger, that meant that moving left and up makes them smaller, and since x = 0 and y = 0 is the smallest number you can have without going negative (and you can go negative by the way), it makes sense for that to be the top left corner
</p>

<p>
This is can be really confusing because it's different from it's different from what you'll learn in your math and physics classes. In school, going up increses y (not down), and the x = 0 and y = 0 point is in the bottom left (not top left). So make sure flip the switch when back to "up is more" when you go back to school, I hate for this course to hurt your grades
</p>

> TODO: image of the x/y axis from school

<p>
Another handy trick for viewing x, y is to look at the bottom left of the room editor. There you see 2 numbers (for example ``(841, 592)``). Those represent the x, y position under your mouse cursor, and you can that to again verify the following:
</p>

<ul>
 <li> Moving the mouse left decreases x </li>
 <li> Moving the mouse right increases x </li>
 <li> Moving the mouse up decreases y </li>
 <li> Moving the mouse down increases y </li>
 <li> Moving the mouse to (0, 0) is at the right spot </li>
</ul>

> TODO: gif moving the mouse, along with the numbers in the bottom left

</details>

## Variables

Cool, now that you know what x and y are, we can start updating them through code
In coding terms these are actually called variables, so I'll use that term from now on.
