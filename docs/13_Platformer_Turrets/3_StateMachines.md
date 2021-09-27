---
layout: default
title: State Machines
nav_order: 3
parent: Platformer | Turrets
---

# State Machines

State Machines are where you break your code up into a series of states, and then formally define behaviours/transitions for each of them. I mentioned these in "Platformer | Character Animation | Multiple Sprites", but now we're ready to dig in. Let's do it!

## The problem with flags

Now that we've got some flag practice in, I wanted to go over some pros and cons. Flags are a nice quick way to add behaviours/features to your game. You just define a flag, and then go to all the relvant code and add ``if(flag){ /* do new thing */ }else{ /* do the old stuff */ }``, but they tend to get messy as your game grows

My favorite example is from when I worked on a fighting game. I had flags for a bunch of things (puching, kicking, blocking, etc). Everytime I added one, I had to update a bunch of areas of code to make sure the flags were toggled correctly, and that the correct behaviours were enabled disabled. I'd end up with if statements like this:

```
if(punch_pressed and not is_kicking and not is_blocking ...){
	is_punching = true;
}
```

and my if statements would just keep getting longer as I added more flags. Also I often wouldn't reset them properly and I'd end up with scenarios where both ``is_punching`` and ``is_kicking`` are set to true (which sounds cool, except that it wasn't actually a thing in our game 😪 )

How do we fix this!!! Let's find out in the next section

## Introducing state machines

State machines to the rescue 🦸‍♀️!!!

The key insight with the fighting game example, is that punching, kicking, and blocking are all mutually exclusive (meaning there can only be 1 set at a time). So rather than manage them each individually, I just had one state variable and then move it between moving, kicking, punching, and blocking. Now instead of doing ``not is_kicking and not is_blocking ...`` everytime I wanted to punch, I could just do ``state == moving``. Also having one variable ensures that I can only be in one state at a time

> **So no more flags!?!** No, flags are still great, but if flags are mutually exclusive then you can often convert it to a state machine. But even then, it depends. You'll just have to go with which ever you think is the most intuitive for the current scenario

So how does that apply to the current project? So we have the is_hurt and is_dying flags, let's change those over to the in_control, is_hurt, and is_dying states

```
// oPlayer Create Event
enum PLAYER_STATE{
	in_control,
	hurt,
	dying
}
state = PLAYER_STATE.in_control;
// you can remove the is_hurt and is_dying VARIABLES

// oPlayer Step Event
//// ANIMATION
{
	// death
	if(state == PLAYER_STATE.dying){
		...
}

// oMissile Step Event
//// HURT PLAYER
{
	if(place_meeting(x, y, oPlayer) and oPlayer.state == PLAYER_STATE.in_control){
		with(oPlayer){
			hp -= 10;
			state = PLAYER_STATE.hurt;
			alarm[0] = 10;
		}
		instance_destroy();
	}
}

// oPlayer Alarm 0 Event
state = PLAYER_STATE.in_control;

// oPlayer Draw Event
//// SET COLOR
gpu_set_fog(state == PLAYER_STATE.is_hurt, c_red, 0, 1);
```

Here's the first half of our state refactor logic, enums are the biggest new thing here

## enums

``enum PLAYER_STATE``: Here I'm making a new enum. You can kind of think of enums as grouping a bunch of related variables, and then numbering them sequentially. The snippet below is basically the equivalent of what the enum is doing. When defining enums, you usually don't specify the values of the variables because they don't really matter, it's only important that they're different. We could have used standard number or strings to represent state, but enums give us the best of both worlds. ``state == "in_control"`` is readable, but then you need to be careful to spell your states the same way everytime. ``state == 0`` works, but then we need to remember which state corresponds to ``0``. If we used the example below to have a bunch of variables ``state == PLAYER_in_control`` would give us similar readability to an enum, but using an actual enum is just a cleaner way to initialize (or rather ``enum``erate) all the options together

```
// equivalent to the enum declaration
PLAYER_STATE_in_control = 0;
PLAYER_STATE_hurt = 1;
PLAYER_STATE_dying = 2;
```

``state = PLAYER_STATE.hurt;``: From there I'm updating all the flag checks/sets to be state checks/sets instead. So ``is_hurt == true;`` becomes ``state = PLAYER_STATE.hurt;``

``oPlayer.state == PLAYER_STATE.in_control``: This is actually a new addition from the new code. Now that we formalized our states, and I'm revisiting this code I realized that the missiles could have interrupted our dying animation and moved us back to the hurt state. Earlier I would have fixed this by doing ``not is_dying``, but now that we're working with states, we also need to ask our selves what we think should happen if the player is already hurt. I think it makes sense to cancel new damage if you're already in the middle of taking damage, so I've updated the if statement to only apply damage if the player is ready for it. So this is a happy side effect of using states, having a clearer sense for the system helps us considered edge cases that we wouldn't have thought about before

## switch statements

Here's one way I could have refactored our step event

```
if(state == PLAYER_STATE.is_dying or state == PLAYER_STATE.is_hurt ){
	exit; // do nothing
}else if(state == PLAYER_STATE.in_control){
	//// HORIZONTAL
	//// VERTICAL
	//// SHOOTING
}

// ANIMATION
```

So here we have a single variable, ``state``, and we need to break out cases for how we'd handle each possible value of state. This is the exact scenario that switch statements are made for. Let's see how we'd write the same code using switch

```
// oPlayer Step Event
switch(state){
case PLAYER_STATE.hurt:
case PLAYER_STATE.dying:
	// do nothing
	break;
case PLAYER_STATE.in_control:
	//// HORIZONTAL
	//// VERTICAL
	//// SHOOTING
	break;
default:
	print("STATE NOT HANDLED", state);
}

// ANIMATION
```

``switch(state){ .. }``: Here we want to check all possible values of ``state``, so we specify state here since that's the value we're ``switch``ing on

``case PLAYER_STATE.in_control:``: Game Maker will skip over all switch code until it finds the case that matches the current value. So whenever ``state == PLAYER_STATE.in_control``, it'll trigger this case, and it'll execute the code we defined below it

``case PLAYER_STATE.hurt:`` / ``case PLAYER_STATE.dying:``: If multiple cases have the same code, then we can list them together, and the same code will be triggered regardless of which case matched it. So if ``state == PLAYER_STATE.in_control`` then ``case PLAYER_STATE.hurt:`` will be triggered and it'll continue to fall through ``case PLAYER_STATE.dying`` and execute whatever code comes up (in this case it's just ``// do nothing`` but you get the idea)

``break;``: This is the easiest thing to mess up in your switch statements. A side effect of the fall through logic is that the game doesn't know when to stop executing the case code, so you need to include ``break;`` at the end of your code. For example if we didn't include ``break;`` after ``// do nothing``, then it would have just continued past ``case PLAYER_STATE.in_control`` and executed all the code there

``default:``: If the switch passes all the cases without any matches, then it'll fall under default. In this case I've included all the cases, so the default should never trigger. I have the print statement there so that I'll know if it triggers in case something goes wrong

Now that I've shown you how to refactor the step event with a switch, can you do the same for the draw event?

<details data-summary="How to refactor draw event using switch?" markdown="1">
```
// oPlayer Draw Event
switch(state){
case PLAYER_STATE.dying:
	//// DEATH
	break;
case PLAYER_STATE.hurt:
case PLAYER_STATE.in_control:
	//// SET COLOR
	//// ARMS
	//// RESET COLOR
	break;
default:
	print("STATE NOT HANDLED", state);
}
```

This is a very similar structure to the previous one. But instead of grouping ``dying``/``hurt``, I'm grouping ``hurt``/``in_control``. Luckily it's very easy to move around the cases when you're working with switch
</details>

After all this refactoring our game doesn't really look any different, but that's just life as a game designer/programmer. We know we did something special here, and that's what's important 😊

## Animation state machine

I initially referenced state machines in "Platformer | Character Animation | Multiple Sprites", but you may have noticed that I left that code the same. Could/Should we also refactor that part? We actually have a couple of options here (and as a warning, consider this whole section to be 🤓)

**Extend the current state machine**: One option is to add more states for falling, idle, etc. This would basically be breaking up in_control into multiple more granular states

**Animation vs. Behaviour States**: I generally find that Animation and Behaviour tend to have a similar, but annoying different set of states. So in this example in_control was enough from a behaviour stand point, but the Animation has multiple sprites/states to select between for that state. One option is to think of them as 2 different sets of states, I'll talk through that next

**Parallel State Machines**: We could define ``PLAYER_STATE`` and ``PLAYER_ANIM_STATE`` as separate enums, and manage them independently. In this case they'd be the same except ``PLAYER_ANIM_STATE`` would have some extra states. That'll make ``PLAYER_STATE`` easier to work with since we don't need to think about all the extra animation states

**Sub State Machines**: We could also define mini state machines that get run specifically when we're in specific states on the main state machine. So we could define falling, idle, moving, etc to part of the ``PLAYER_MOVE_STATE`` states, and these only get used when we're on ``PLAYER_STATE.in_control`` in the main state machine

**Over-Engineering**: Or, a radical thought, we could just leave it as is? Stick with the general state machine, but leave the animations as a chain of if statements? Over-Engineering is when you apply a complex pattern to a simple problem and the complex pattern ends up hurting your code more than it helps. Always keep in mind that these patterns are meant to make your code easier to maintain, and if they're not doing that then they're overkill.

**So what should we do?**: Personally I've never felt the need felt the need to have multiple state machines, my code usually ends up in the current state, and I don't see much benefit in adding more state machines. That said, if I had a more complex/dynamic system, I would probably reach a point end up picking a more complex pattern accordingly, but I just haven't ever gotten to that point
