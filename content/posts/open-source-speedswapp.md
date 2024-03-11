---
title: "Why I'm open-sourcing Speedswapp"
date: 2024-02-28T10:47:47+01:00
draft: false
---

**Update: reword the list since it contained a typo**

Apple made an announcement not too long ago. In this announcement they told us that in the newest version of iOS PWA's would no longer be supported due to some concerns of security. They would have to let other browsers run PWA's on their on phones, which they used as an excuse to axe the feature. Mind you they have never fully supported PWA install and this should come as no surprise, but this means a couple of things for developers.

# The consequences
So now for a developer to put an app on iOS they will have to buy a mac, and use this mac to build fully native apps, as they have disabled the option to use webview applications which just load the PWA. I am one of the developers impacted as I wanted to release a PWA of my car social media website, called Speedswapp. This will no longer happen because:

1. I refuse to buy a mac specifically for one project
2. The tech stack I worked with is not compatible, so I'd have to rewrite most of the app
3. I don't want to worry about legacy versions using native applications

These reasons mean I am, for the time being, no longer actively developing speedswapp as the app I envisioned it to be. I will still work on it to serve it up as an open source social media template for Elixir and Phoenix.

# Conclusion
All the above reasons and explanation culminates in me open-sourcing speedswapp as-is, for people to learn Phoenix and Elixir from. Now some expected features have not yet been implemented since I did not envision them being part of the MVP, like comments, likes and infinite loading, so feel free to create an MR to help people learn Phoenix in a real project. 

[Check it out on Github!
](https://github.com/JoeriDijkstra/speedswapp)
