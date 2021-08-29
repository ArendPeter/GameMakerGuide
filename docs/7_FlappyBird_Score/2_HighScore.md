---
layout: default
title: High Score
nav_order: 2
parent: Flappy Bird | Score
---

# High Score

Next let's display your score (i.e. distance flown), and then keep track of high scores (even after closing the game 😲)

## Basic High Score

Let's add a quick and dirty high score system

```
// oControl Create Event

...
high_score = 0;

// oControl Draw GUI

//// LIVES
//// HIGH SCORE
draw_set_halign(fa_left);
draw_text(100, 10, "Score: "+string(oBird.x));
draw_text(100, 40, "High Score: "+string(high_score));

// oBird Step Event

...
//// DEATH
{
    colliding_with_pipe = place_meeting(x, y, oPipe);
    if(colliding_with_pipe){
        ...

        if(x > oControl.high_score){
            oControl.high_score = x;
        }
    }
}

```

``high_score = 0;``: ``high_score`` is another persistent variable, so we'll define it in ``oControl``

``draw_text(100, 10, "Score: "+string(oBird.x));``: The score is just how far the bird travelled, so we can conveniently use x for that. I want to prefix the score with "Score: ", but unfortunately we need to do the string concatenation to make that work. You can refer back to "Flappy Bird | Movement | Jump" to get a refresher on that

**TODO** hyperlink "Flappy Bird | Movement | Jump"

``oControl.high_score = x;``: When the bird dies, we want to check if our current score is better than the high score, and then update the high score accordingly

``room_goto(rmMainMenu);``: This is a line of code that was included in the previous section. I mentioned I optioned for that approach instead of ``game_restart();`` to make the high_score easier. Well, ``game_restart();`` would have restarted the game at ``rmStart`` and oControl would have also reset the ``high_score`` in that case. Using ``room_goto(rmMainMenu);`` ensures that the highscore persists after the bird runs out of lives

When we test this we should see the high score update everytime we die 😄

![](../../images/flappy_bird/high_score.gif)

## Save File

Usually when you think of high scores, you expect them to stay around if you close the game, and come back later. Otherwise, how would you show off to your friends? I'll cover how to do that next

(Warning, this is more detail than you need but hopefully it's still interesting 🙃). When the game is running, and you're updating your variables, your computer is saving them to your RAM, which is a form of temporary memory. RAM is great since it's fast (storing variables takes less than a nano second), but the temporary part is the problem. If we want to keep it around when you close the game, it needs to be saved to a more permanent place, like your hard drive. Updating the hard drive is slower (measured in milliseconds instead of nano seconds), but at least the data stays around after you turn off your computer 😊. All of this is a long way of justifying the need for save files

There are many types of save file formats, but game maker has some handy functions for ini files, so we'll use that. Here's the code

```
// oControl Create Event
...
ini_open("save.ini"); // game maker is just like you, it need to open them first before reading 😉
high_score = ini_read_real("save1", "high_score", 0);
ini_close();

// oControl Game End Event
ini_open("save.ini");
ini_write_real("save1", "high_score", high_score);
ini_close();
```

``ini_open("save.ini");``/``ini_close();``: Whenever we're updating the save file we need to use these functions to open and close the file (just like when we're updating files 😉). For ``ini_open()`` you also need to specify which file we're editing (in theory we could manage multiple ini files, but I've never felt the need)

``ini_read_real("save1", "high_score", 0);``: We use this to load numbers from the ini file. The first parameter, and second parameters are the section and entry names.  So we're looking up the "high_score" entry, which is under the "save1" section (we're not using multiple sections, so I just used a non sense name for the section). Then the third parameter is the default value if the entry doesn't already exist. So if we're starting the game for the first time the high score will initialize to 0

``ini_write_real("save1", "high_score", high_score);``: This is how we add numbers to the save file. Similar to reading, except the 3rd parameter is the value we want to write to the save file. Make sure that the first 2 parameters match between the reading and the writing functions, otherwise you'll be writing/reading from different places

Now, play the game, get a new high score, then close and reopen the game. When you play it again, you'll notice that the high score is still there 🤯

> And just to be thorough, I want to mention that you also have ``ini_read_string()`` / ``ini_write_string()`` avaialable incase you want to try that

## Verify save file

Just to show you that an actual file is changing, here's how you can find the file on both windows and mac

![](../../images/flappy_bird/windows_save_file.gif)

![](../../images/flappy_bird/mac_save_file.gif)

> For windows I chose to type the path instead of navigate there because the AppData directory is hidden by default on most windows machines

You should see something like this

```
[save1]
high_score=253
```

This is not interesting to see in action, but it's also useful when debugging. For example, this is a really handy way to see if you have a type in your variable name

## Further Optional Tweaks

So, we got lives working, and we got high score working 🎉. But I'm not totally satisfied with the high score system. This approach would work great if we only had a single room, but it's kind of weird in a multi room situation

Our score is just the player's ``x`` position, so if you beat the first room, and go to the next room, your score just resets back to 0. I feel like it should continue

One way to fix this is make a current_score variable in ``oControl``. Then you can add ``current_score += oBird.dx;`` to the ``oControl`` step event. This way it increases at the same rate as ``x``, but doesn't reset in the next room

But the problem there is the score will still start and 0 when you select a later level from the level select menu. Maybe that's not a problem? I guess more or less what **Spelunky** does. Player's have the option to skip to later sections, but they need to start from the beginning to maximize their score

If you wanted to select level 2 and start with the score from level 1, then that gets a lot tricker. One way to do it is add another variable definition to ``oButton`` indicating what the starting score will be. Then you can manually specify what the correct value should be

But that solution is hard coded, so you'd need to manually adjust the numbers if you resized some of the rooms

You might be able to find a solution ``room_get_next()`` and ``room_get_width()`` to dynamically add up the widths of all the rooms, but I can't think of a way to do that without it getting very complicated

Anyway, I think I'll leave this as an optional exercise for you. There are a lot of ways to go about this (both from a design perspective, and a technical perspective), and I'll let you decide how far you want to follow this rabbit hole
