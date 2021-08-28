(I'll figure out what to do with this later)

# Angle

Now let's add a little trick to make the bird face upwards whenever it's moving up, this adds a lot to the look and feel considering how small it is

## image_angle

Here's our code

```
// oBird Step Event

//// DEATH
//// GRAVITY
//// JUMP
//// APPLY MOVEMENT
//// ANGLE
{
    if(dy < 0){
        image_angle = 20;
    }else{
        image_angle = 0;
    }
}
```

``image_angle``: is the key variable you can use whenever you want to rotate your sprite. The numbers are the same as the ones used by the direction. Default, ``0``, is to the right, and then it goes counter clockwise from there all the way to 360. We always make our character sprites face to the right for this reason, it just makes rotation much more intuitive (the birds face corresponds with the angle)

![](../../images/pong/direction_circle.png)

That said, the above code should make the bird face upwards if the dy corresponds to the bird moving up

![](../../images/pong/flappy_bird_angle.png)

## show_debug_message()

Let's say we wanted to get more insight into the angle logic, just to make sure it's working as we expect

For the moment it's tricky, because the game is moving so fast. One way around that is to use ``show_debug_message()``,

## print()
