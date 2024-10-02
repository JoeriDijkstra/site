---
title: "Dragonhoard Release"
date: 2024-10-02T18:53:20+02:00
draft: false
---

Dragonhoard just had an exciting new update, version 1.2.4 released today. The main selling point of the release is that it now supports mobile and adds PWA support. In this blog post I'll share a little bit of my experience creating Dragonhoard.

## Coming up with the premise
The premise of Dragonhoard is simple, an anarchic inventory management system where everyone has equal rights. Every item has an owner which can be transfered and a holder which is currently in posession of the item. I came up with the premise in a Dungeons and Dragons session, we have a group all with their own campaigns and sometimes we do oneshots.

To be more cost effective we share a lot of resources like minis, mats and terrain. To manage this properly I created dragonhoard, since here we can request items from the holder to bring it back or give it to someone else. Of course this can be used for any kind of item.

## The tech stack
Dragonhoard was built in Elixir with Phoenix liveview. The source code is available in Github and available to everyone. It is then hosted on Fly.io, and you could run your own instance if you want.

## Making a PWA
Making a PWA from a Phoenix Liveview application is relatively easy, all you need to do is create the manifest.json file detailed in many other blogs, let's focus on Phoenix Liveview for now. The most important thing is to put the manifest.json in your assets folder, and then add it as a static path.

Open up the file `lib/your_app_web.ex`, the web file, and add the manifest.json to the static paths, like so:
```
def static_paths, do: ~w(assets fonts images favicon.ico robots.txt manifest.json)
```

And there you go, your manifest is now static and is loaded in for your web application, but don't forget to add it as a link in the `root.html.heex` file in the head, like this:

```
<link phx-track-static rel="manifest" href="/manifest.json" />
```

And there you have it, a loaded manifest in your Phoenix Liveview application, which just made sure the app is loaded as a PWA.
