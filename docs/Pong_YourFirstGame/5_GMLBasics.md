---
layout: default
title: Getting started with GML
nav_order: 5
parent: Pong | Your First Game
---

# Getting started with GML

The the previous section we just had a BIG first, we ran our game for the first time :D. This section will be a rapid fire of more BIG firsts. Here's the list

 * We'll change instance positions with code 😎
 * We'll use keyboard input to make the game interactive 😲
   * and we add local co-op while we're at it 😵
 * We'll make instance interact with each other 🤯

That's all on this page!! Let's do it!

## x / y axis

Let's look back at the instance properties in the room editor. Earlier we modified the scale x property, but there's much more where that came from and we'll quickly become familiar with all of them throughout the course. The most important properties here are the x and y, so dig into those next. You were probabaly wondering what the X by Scale X meant. x and y are pretty much just short hands for horizontal and vertical respectively. So why didn't they call it horizontal scale, and vertical scale instead? Well we refer to x and y so often that they it wouldn't quickly become tedious to always need to say horizontal or vertical.

> TODO: png of instance properties

So with that said the x and y variables represent where each instance is positioned in the room horizontally and vertically. If you drag one of your instances to the right you can see that the x increases. Similarly if you drag the instance down you see that y increases. I can also set the x and y directly in the instance properties.

> TODO: gif moving instance left/right

If I set it to x = 0, and y = 0, where do you think the instance will move to? (I'm hiding the rest of the section for you to think about it 😉 )

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

## Events

We're very close to digging into code, but first we need to understand events. Code is our tool to describe "What" should happen, but events show us "When" it should happen. For example, if we go to the ball object, and click "Add Event" we can see lot of options.

> TODO: gif of navigating to the object and addinga n event

Luckily we only usually need a few of these. These are the common ones we'll use today

 * **Create**: This is triggered when the instance is first created
 * **Step**: This is triggered every frame of the game (by default 30 times per second, but this can be tweaked)
 * **Keyboard events**: These are triggered in reseponse to your keyboard inputs

For now, let's add a **Create** event. Once you've done that you should see a text editor pop up for us to start entering a code (the natual "What" that triggers from the "When")

## Variables

Yay! we can finally start coding (it's like my favorite thing 🥰). Since you now know what x and y are, let's make that our first coding task!

In coding terms, all the instance properties we were working with are actually called variables, and they each correspond with a variable name in GML. We'll be working with the variables for X and Y, which in code are just ``x`` and ``y``.

Here's how you assign values to variables in code

```
variable_name = new_value;
```

Example

```
x = 300;
```

The above code sets the ball's x to 30. We can run the game to verify this but before we do, try predicting where the ball will show on the screen (maybe you can use the mouse trick I showed you in the x/y axis section?). When you're ready run the game and see if you're right

<details>
<summary> <b> Where will the ball be placed? </b> Expand to find out

<p>
If you said "center leftish", you'd be right!!
</p>

 > TODO: insert png with the game and the ball position

</details>

Before we move on to adjusting more variables, I have some more details to share on assigning variables

 * **Assignment Operator**: In code, we call `=` the Assignment OperatorWe can think of the variable name on the left as a box, the assignment variable moves the value on the right into the box. Unlike math there's no rule that the contents of the box has to be the same as the value for the statement to be valid (although they will be the same once the value is moved into the box). For now these differences are subtle, but they'll be important to understand later
 * **Semi Colons**: The semi colon, `;`, marks the end of the statement, think of it like the period at the end of a sentence, it represents the end of a complete thought 😊. We use them here for the assignment statement, but we'll also need it on all future statements. Interestingly, you can actually remove semi colons, and your code will probably still work fine. However there's some specific situations where things could break, and if you hit those situtations, it will be VERY hard to find the issue. For this reason yoyogames STRONGLY recommends using semi colons, so this is a good habit to keep.
 * **Spacing**: I included a single space on either side of the equal sign, but I don't have a space before the semi colon. I almost always do it this way to keep things consistent, but Game Maker doens't actually care at all. You can add as many spaces, tabs, or even new lines around you code, and Game Maker will handle it the same. This will be particularly useful when we start getting into functions later. Below are some examples with our assignment statement.

```
x = 30;
```

```
x=30;
```

```
        x      =           30             ;
```

```
x     
=
30
;
```

## Comments
## Keyboard Input
## Ball collision
## Randomize ball speed
## Reset the ball
