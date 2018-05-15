---
layout: post
date: 2016-05-15 16:00
title:  "Expanding context in diffs"
category: 
- tips
tags:
- git
- code-review
- diff
---

Whether it’s reviewing open source project, or using git on a private project, we all spend a lot of time looking at diffs. Although some prefer graphical interface clients I use plain command line.

But to really understand a code change, we sometimes need to see more than just the default three lines of context especially in cases when long method names.

That's when this git option come in handy:

<code>git diff -U15 app/models</code>

It generates diffs with 15 lines of context instead of the usual three.

Depending on your commit you may set the most appropriate number for lines of context to get the exact amount of context info.
