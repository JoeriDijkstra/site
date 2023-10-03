---
title: "Simplicity in web development"
date: 2023-10-03T20:29:42+02:00
draft: false
---
    
In this post I would like to discuss simplicity in programming and what that means to me as a web developer. Granted I have mostly worked as a backend engineer but in smaller applications I have been responsible for front-end as well. The points I am going to talk about are very general and I will not be able to cover all my thoughts in this post, so I will scope this specifically to a very popular subject, namely simplicity in web design.

# Simplicity in web design
Look at another tab you have open. If it is not arxiv or hacker news chances are that the website is "modern" and "sleek". Think about what you're trying to do on that website, chances are you are trying to find some information or share/update some information. After all, most websites are really nothing more than that, some crud operations behind a nice (although samey) facade. How long does it take you to do this? How clear is it for you to find what you want? 

## Comparison
Let's pick a programmers greatest friend (which has been around for years) [hackernews](https://news.ycombinator.com) as an example for how I think it should be done. You have a single page rendered by the server with a list of links. You can click the link or find other links from the site. Then down you see the points, amount of comments and details, where if you click on them you can read them. Genius right?

Now let's try a newer website which is supposed to be sleek and which a lot of the same target audience has to use. Let's compare it with [JIRA](https://www.ifuckinghatejira.com) shall we? My biggest gripe with JIRA is that everything is in context of eachother. A button does not necessarily do what you expect it to do, or what you thought it would do. For instance, changing the issue type (updating information) does work in the breadcrumbs, but impossible to do in the 'linked issues'. Or in the sprint overview. If a button exists it should work the same way everywhere within the application.

Also between the two there is an enormous difference in load times, while the amount of data from JIRA is more of course, it is not an excuse for the extreme slowness and lazy-loading that is done on the site. For now I will keep my thoughts on actual simplicity in development out of scope.

## Takeaway
To sum up, my most important belief is that readability and usability should always be on top. Every website is a facade querying and mutating data and should not be seen as any more than that. Make it as fast as possible, and as small as possible. Server side rendering is the best option in most cases, which I will elaborate on more later.
