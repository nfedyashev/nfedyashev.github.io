---
layout: post
date: 2019-09-21 16:00
title:  "How to find Git commits from the past week"
category:
- tips
tags:
- git
---

You may find this little snippet useful for your weekly stand up reports.

This command counts all commits authored by "Joe" in the currently opened branch:
```bash
git log --since=7.days | grep Joe | wc -l
```
&nbsp;

This command counts all commits authored by "Joe" in all branches:
```bash
git log --since=7.days --all | grep Joe | wc -l
```
