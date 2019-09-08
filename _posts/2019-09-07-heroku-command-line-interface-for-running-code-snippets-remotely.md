---
layout: post
date: 2019-09-07 16:00
title:  "Heroku command-line interface for running code snippets remotely"
category:
- tips
tags:
- heroku
- cli
- rails
---

This blog post is an explanation about a method for executing one-off
scripts and snippets on Heroku that might be better than other
solutions.

## Starting with drawbacks of alternatives

1) pasting prior written code into running `heroku run rails console`
console.
Depending on settings of your editor/IDE/clipboard-manager you may
experience some issues trying to run this code. There is a chance of it
being corrupted and not looking like a valid Ruby code, especially if it
is a long multi-line snippet.

2) adding this code as a temporary method/class and running it in
production like `heroku run lib/temp_snippet.rb`.
It generates additional commit and a deploy(although these 2 might be
important logging artifacts in some cases). What's definitely not
desirable is some downtime caused by that deploy(unless you use [Heroku
Preboot](https://devcenter.heroku.com/articles/preboot))

3) Running it as regular Rails db migration has the drawbacks of (2)
plus additional maintenance cost.

## An alternative is to run your snippet like so:

```bash
cat foo.rb | heroku run console --app=bar --no-tty
```

<!--more-->

For the past couple years I use this method for anything beyond a couple
of lines long commands(which I still run in interactive rails console).
So basically this method gives you:

 * a convenient way to write and test your script locally
 * a reliable way to execute it in production
 * it might be version controlled
 * doesn't cause any re-deploy downtime
