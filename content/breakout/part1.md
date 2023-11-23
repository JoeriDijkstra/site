---
title: "Introduction and scoping"
date: 2023-10-10T21:58:06+02:00
draft: false
---

The famous ID software developer John Romero once said in a very interesting [interview](https://howtomarketagame.com/2023/09/25/john-romero-on-his-book-doom-guy-and-developing-games-at-a-small-scale/):
> We knew how to scope. We knew what felt like a good size, so when we scoped it out, there were only 16 levels (to be made)

So let's apply this principle to our own game. Luckily we know exactly what breakout looks like, it is a ball, which you hit with a paddle. The ball has collision and should destroy the blocks and give you a score. I italicized a few things I think are going to be important. Let’s make a list:

1. Ball - This will be a moving cluster of pixels, which should spawn at a random x location on the screen, with a Y of zero. Should be faster every bounce.
2. Paddle - The paddle is a rectangle with a certain width, which you should be able to move to either the right side or the left side. Should get smaller every bounce.
3. Collision - The ball and the paddle should not be able to leave the screen. The ball should bounce of it.
4. Blocks - The blocks should be placed in a certain pattern of 4 rows. They are destroyed if the ball bounces off of it.
5. Score - There is 1 point added to the score if a block is destroyed.
 
These are the parts we are going to program over this journey. The entire course should not take more than an hour to complete. Let's get started by creating a makefile and setting up our renderer, before we can get into the more fun parts of the programming.

[Next up: Creating the makefile]({{< ref "/breakout/part2" >}} "Part 2: Creating the makefile")
