---
layout: post
date: 2018-03-15 12:36
title: "Describe PostgreSQL table from command line"
description: After using annotate gem in plenty of projects and thinking about it I would prefer not too use it in future projects. Here's why
comments: true
category: 
- tips
tags:
- postgresql
- activerecord
- annotate
- db-schema
---

You may be familiar with `annotate` gem(<a href="https://github.com/ctran/annotate_models">GitHub</a>, <a href="https://rubygems.org/gems/annotate">Rubygems</a>). Neat library that adds comments summarizing the current DB schema to the top of each of your model/factory/spec.
First time I've used it in production project(5+ yrs ago) it felt really handy, especially in big apps with lots of models and fields. I no longer needed to keep in my mind certain field names neither needed I to look them up in DB administration tools. Big productivity boost.

After using this whole model annotation concept in plenty of projects and thinking about it I would prefer not too use it in future projects. Here's why:

<!--more-->

* it increases length of your models, first thing that you do when you open a file is to scroll down to actual code that you care about. A bit annoying and slightly slows down your workflow when you think about how many times you do it every day.
* it needs to be updated and kept in sync with you code updates. Yes, you can automate it as part of your <a href="https://github.com/brigade/overcommit">overcommit</a> setup but it also takes time and can be a bit annoying when you need to switch between branches.
* it makes commits/pull requests a bit more verbose and repetitive then they could to be e.g. one modified line of code could come with 3+ modified annotation lines.
* in case of automated annotation it become really annoying when you need to switch between various gems with different database schema versions.

So what I prefer to use these days? Just open up a separate tab and used psql's CLI interface for the model I would like to describe:

{% highlight shell linenos %}
psql foo_development -c  '\x' -c '\d+ books'
{% endhighlight %}

<a href="{{ site.url }}/assets/describe.png" target="_blank">
	<img width="250" src="{{ site.url }}/assets/describe.png" />
</a>

This command could be easily found in your bash/zsh history using reverse search or you can just extract it into a shell function like so:

{% highlight ruby linenos %}
function describe_table() {
  psql eth_farm_development -c  "\x" -c "\d+ $1"
}
{% endhighlight %}
And call like: `describe_table books`

Trying to recall how many time I need to look up model schema and it looks like just a fraction of time spent on all other activities. So for me it's 

Desire to look up table schema is such a rare occasion in comparison with all the other tasks that you need to perform that it doesn't make sense to pay such a "high price" for low benefit if there is a simple workaroudn.

Just compare how often do you need to see model's schema in comparison with all the other tasks that you constantly do in you .

One of the main reasons I find this approach more desirable is because seeing this kind of info is such a rare activity in comparison with all other tasks that you need to do.