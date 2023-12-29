---
title: "Configuring the tab-line in Emacs"
date: 2023-12-26T13:04:07+01:00
draft: false
---

When you use a different editor than Emacs you might get used to some creature comforts, like for instance being able to have tabs open. Emacs uses buffers where you can switch between them using `C-x b` to open a searchable list containing all your buffers, and that's how you switch tabs. This is not neccesary however and there are more modern ways of attacking this problem.

# What is the tab-line?
Put simply, the `tab-line-mode` allows you to switch your buffer within a window. You get working tabs like you would expect a web browser to have. If you open a new buffer in your window, it gets added to the `tab-line`. **Note:** The tab line will not include all your buffers, and you can close them as you please. The state of each tab-line is stored in the window.

# Configuring the tab-line to automatically start
The command `global-tab-line` toggles the `tab-line` on every buffer you have open or will open. So we want to call this command to the config:
```elisp
(global-tab-line-mode t)
(setq tab-line-new-button-show nil)  ;; do not show add-new button
(setq tab-line-close-button-show nil)  ;; do not show close button
```

I prefer not having the buttons, since we automatically add new tabs when we open a file and you can close it with middle mouse button.

# Styling the tab-line
We can style the tab in the config using the `set-face-attribute` command in the config. You can read more about face attributes [here](https://www.gnu.org/software/emacs/manual/html_node/elisp/Face-Attributes.html). I am going to style it like the Monokai pro theme which I personally use. Here is a sample:

```elisp
(set-face-attribute 'tab-line nil ;; background behind tabs
      :background "#1e1e1e"
      :foreground "gray60" :distant-foreground "gray50"
      :height 1.1 :box nil)
(set-face-attribute 'tab-line-tab nil ;; active tab in another window
      :inherit 'tab-line
      :foreground "gray80" :background "#2e2e2e" :box nil)
(set-face-attribute 'tab-line-tab-current nil ;; active tab in current window
      :background "#b05279" :foreground "white" :box nil)
(set-face-attribute 'tab-line-tab-inactive nil ;; inactive tab
      :background "#2e2e2e" :foreground "black" :box nil)
(set-face-attribute 'tab-line-highlight nil ;; mouseover
      :background "white" :foreground 'unspecified)
```

For now, I am pretty happy with how it looks, using the Monokai colors.

# Further reading
- https://www.gnu.org/software/emacs/manual/html_node/emacs/Tab-Line.html
- https://www.gnu.org/software/emacs/manual/html_node/elisp/Face-Attributes.html
- https://amitp.blogspot.com/2020/06/emacs-prettier-tab-line.html
- https://andreyor.st/posts/2020-05-10-making-emacs-tabs-look-like-in-atom/