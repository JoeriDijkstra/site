---
title: "Introduction and scoping"
date: 2023-10-10T21:58:06+02:00
draft: false
---

The famous ID software developer John Romero once said in a very interesting [interview](https://howtomarketagame.com/2023/09/25/john-romero-on-his-book-doom-guy-and-developing-games-at-a-small-scale/):
> We knew how to scope. We knew what felt like a good size, so when we scoped it out, there were only 16 levels (to be made)

So let's apply this principle to our own game. Luckily we know exactly what breakout looks like, it is a *ball*, which you hit with a *paddle*. The ball has *collision* and should destroy the *blocks* and give you a *score*. I italicized a few things I think are going to be important. Let's make a list:

2. Ball - This will be a moving cluster of pixels, which should spawn at a random x location on the screen, with a Y of zero. Should be faster every bounce.
3. Paddle - The paddle is a rectangle with a certain width, which you should be able to move to either the right side or the left side. Should get smaller every bounce.
4. Collision - The ball and the paddle should not be able to leave the screen. The ball should bounce of it.
5. Blocks - The blocks should be placed in a certain pattern of 4 rows. They are destroyed if the ball bounces off of it.
6. Score - There is 1 point added to the score if a block is destroyed.

We got 5 individual points which are scoped, but we are missing the 1st. This 1st point will be to install all necessary programs. I will be using emacs for this project, but you can use any editor you want.

1. Install necessary programs - this would be an appropriate C IDE or LSP, the C compiler, MAKE and SDL

Let's dive in and get our hands dirty.

*Next part expected in the coming weeks.*
