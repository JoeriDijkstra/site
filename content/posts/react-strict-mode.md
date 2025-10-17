---
title: "Double render in React"
date: 2025-10-17T21:12:12+02:00
draft: false
---

Ever have the annoying bug that your component gets rendered twice even though you are sure it shouldn't? Messing around with setting flags to try and only have it run once? Are you getting janky animations on development but not on production?

Well me too, and apparently the issue is very simple. There is a bug in the strict mode.

```
<StrictMode>
  <App />
</StrictMode>
```

And you _can_ just comment out the `</StrictMode>`.

Although this is not something I recommend. Unfortunately I have not yet found a way to solve the problem with strict mode on. for now I have just learned to live with it and sometimes disable it for a demo or when I want to double check if something is working or I do have a mistake in the rerendering.

Not the most helpful article, but now at least you know.

Cheers!
