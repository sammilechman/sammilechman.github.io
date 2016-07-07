---
layout: post
title:  "Vim: Yanking to the system clipboard"
date:   2016-07-06
---

### The Problem
The text I yank in Vim should be available to paste in another application. For instance, I may want to yank a URL from the document I'm working on and paste it into Chrome.

### The Solution
Use the system clipboard by way of the `+` register (or the `*` register, depending on what version of Vim you're using).

Vim uses a concept of registers to hold the contents of yanks and deletes. When you yank a line (`yy`), it gets placed in the unnamed register. When you paste back in (`p` or `P`), it uses the contents of the unnamed register. If you yank a line, then delete a line (`dd`), then attempt to paste your original yank back in, you'll notice that the contents of the delete overrode the unnamed register. Luckily, Vim has numbered registers too. The `0` register always holds the contents of the last yank, so pasting from that register (`"0p`) will work.

I yank to the system clipboard all the time, so I have a shortcut set up in my `.vimrc`:

```
vmap <Leader>y "+y
```

All I have to do is select the text I want then press `\y` - now I can paste that text anywhere (the leader key is [configurable](http://usevim.com/2012/07/20/vim101-leader/), by the way)

Another handy trick I've found is yanking the current file path to the system clipboard, so that I can easily share files with my coworkers on Slack. Here's what I have in my `.vimrc`:

```
nmap <Leader>cp :let @+ = expand("%")<cr>
```
