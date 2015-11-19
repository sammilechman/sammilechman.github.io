---
layout: post
title:  "Project-wide find-and-replace using the Unix command line"
date:   2015-11-19
---

I switched over to vim from Atom about 6 months ago, but I won't be trumpeting vim's awesomeness today. In fact, this post concerns an Atom feature I miss, **project-wide find-and-replace**. I find myself wanting to use this feature pretty frequently - I'll create a function, write tests for it, and implement it throughout a project, only to realize the function itself could be better named.

What to do? I've seen some vim solutions for this, but they all felt overly complex. Luckily, this isn't too hard to do at the command line. One of the reasons I love running vim/tmux is you're always close to the command line.

#### **TLDR**:

{% highlight bash %}
git grep -l $OLD_TEXT | xargs sed -i '' -e 's/$OLD_TEXT/$NEW_TEXT/g'
{% endhighlight %}

This solution uses a couple of tools: `git`, `xargs`, and `sed`. For the rest of this post, I'll talk a little bit about how each of these tools works, and how they work together in this instance to solve our problem. It's intended as a learning exercise for those who want to know a little more about some Unix tools.

Clearly, this solution assumes your project is using git for version control. The grep tool that git offers is excellent. It has a ton of features, and it's fast. For the uninitiated, grep is just a pattern finder, and git grep is git's version of that tool. In this case, we're using the `-l` flag, which will output a list of the files that contain the pattern that we're trying to replace. Here's an example from the git repository that holds my vim/tmux configuration files:

{% highlight bash %}
$ git grep -l set
.tmux.conf
.vimrc
{% endhighlight %}

So `git grep -l $OLD_TEXT` finds every file that contains the text we're looking for. `|` pipes the output of the grep to xargs, which is the "construct argument list(s) and execute utility" tool. It takes a bunch of output strings (in our case, the files that contain `$OLD_TEXT`) and iterates over them, running a function on each string. Which function? Well, that's what we specify, in this case it's `sed`, the stream editor utility.

`sed` modifies the input and writes the result to the output. We're using `sed` with two flags. The first, `-i`, edits the file in-place. It requires an argument, *extension*. That specifies where it will save backups, in case you don't like the edited results. Here, we passed an empty string, meaning no backups will be saved. Why did we do this? a) We like to live dangerously, and b) Version control.

The second flag, `-e`, appends a command, in this case `s/$OLD_TEXT/$NEW_TEXT/g`. Finally, our substitution command! That substitutes `$OLD_TEXT` for `$NEW_TEXT` (the `/g` means globally).

This approach isn't without its drawbacks. It's not interactive, meaning you can't pick and choose where the changes should be applied. Also, it'll match *any* instances of `$OLD_TEXT`. For instance if you're trying to change the word `person` it will modify the `person` contained within `personal`. But there it is! Project-wide find-and-replace using only the command line and a few simple tools.
