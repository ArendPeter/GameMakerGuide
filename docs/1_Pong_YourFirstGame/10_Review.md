---
layout: default
title: Review
nav_order: 10
parent: Pong | Your First Game
---

# Review: Your First Game

![](../../assets/images/ball_collide.gif)

You've just made your first game in Game Maker! Amazing work 🎉🎈🎊

Let's review what we learned

## Game Maker's Interface

You know the basics of Game Maker's interface

* **DnD vs GML**: DnD (Drag & Drop) is a visual programming language, where as GML (Game Maker Language), let's you make program games w/ code
* **Window Layout**: You know how to manipulate windows within the Game Maker workspace

## Resource Types

You know how to create the following resources, how to use them, and how to name them (or least you know how I like to name them)

* **Sprites**: basically images, ex. `sPaddle`
* **Objects**: defining brains you can attach to the images, ex. `oPaddle`
* **Rooms**: level editors where you can place objects, ex. rmGame

We learned some other related concepts along the way

* **Objects vs Instances**: Objects are like blue prints, and Instances are the copies of the blueprint that exist in game
* **Room Layers**: Rooms are divided into layers. They determine the order instances/sprites/etc are displayed

## Events

We learned that events define when our code is triggered, and learned about the following events

 * **Create**: Occurs once when instance is created
 * **Step**: Occurs on every frame
 * **Keyboard**: Occurs in response to keyboard interactions
 * **Collision**: Occurs when instance overlaps w/ an instance of another object
 * **Outside Room**: Occurs when the instance's position is outside the room

We also learned that Keyboard events can be split into 3 types

 * **Key Press**: Occurs when the key is pressed
 * **Key Down**: Occurs every frame that the key is held down
 * **Key Released**: Occurs when the key is released

## Variables

We learned how to assign variables in code

```
variable_name = new_value;
```

and also learned several built in variables

* `x`: x position
* `y`: y position
* `hspeed`: horizontal speed
* `vspeed`: vertical speed
* `xstart`: starting x position
* `ystart`: starting y position
* `layer`: current room layer

## Spacing

We learned that Game Maker's white space is flexible

```
x=300;
```

```
x     =      300 ;
```

```
x     
=      
300
;
```

We also learned how we can use this to apply tabbing to our code, and also covered the tabs vs spaces debate as a side tangent

## Comments

We learned about the different comment types

* `//`: single line comments
* `/* */`: multi line comments
* `///`: JSDoc comments

## Functions

We learned many functions, and how to call them

* ``game_restar()``: Restarts the game
* ``abs(...)``: Finds the positive version of a number
* ``choose(...)``: Randomly chooses between one of the inputs
* ``instance_create_layer(...)``: Creates a new instance of an object (at the specified x/y/layer)
* ``instance_destroy()``: Self destructs current instance

## If statements

We learned what if statements were and how to use them

```
if( condition ){
  code
}
```

and along with the if statements we learned about comparison operators. These help us form the conditions for our if statements

| Operator | Definition |
|--|--|
|< |less than|
|> |greater than|
|<=|less than or equal to|
|>=|greater than or equal to|
|==|equal to|
|!=|not equal to|

## Variable Scope and the Dot operator

We also chatted a bit about variable scope, and how we can use the dot operator to access variables in different scopes

**Variable Scope**: By default variables are scoped to the instance (i.e. instance variables). So for example, using x refers to the x variable of the current instance, instead of some other instance

**Dot Operator**: When variables are in different scopes, you can use the dot operator to access them (very similar to 's in english). For example, oBall.x would give you the x position of the ball, even when you're running the code from the oPaddle object
