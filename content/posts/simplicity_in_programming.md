---
title: "Simplicity in web development"
date: 2023-10-03T20:29:42+02:00
draft: false
---
    
A frontend is nothing more than a facade for CRUD operations to a database, with maybe some business logic in the middle. With this out of the way, my question becomes, why are websites becoming so hard to use and slow?

## Simplicity
This post emphasizes simplicity in web development, focusing on the technical side of user experience. To me, simplicity means even without prior knowledge, I can easily and efficiently achieve my goal.

## Translating to webdesign
Web design should be clear. If you set the expectation that a button opens a link, it should. Don't surprise users by making it open a pop-up without a hint. 

An example from a website I really like: [arxiv](https://arxiv.org/), it is clear that the goal of this site is to share information. To find this information, there are hyperlinks which are labeled and take you to the part of the site you expect, setting the context. It is like walking into a library and seeing a sign with the different sections so you know where to look. When you have found the information you are looking for there is a **bold** hyperlink which redirects you to the paper on the same site.

## Common pitfalls
Speed also helps in achieving your goal. When loading modern site often there is no easy way to know how to read the information at a glance, because it either shifts or is not yet loaded in. This loading more often than not takes a while because of heaps of javascript to track every move you make, or inefficiently querying the data after the site is already rendered. In my opinion therefore server-side rendering is the best option in *most* cases.
