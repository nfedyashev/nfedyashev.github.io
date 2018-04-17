---
layout: post
title: Bitbucket Gemfile git_source
date: 2018-04-17 16:57 +0300
comments: true
category: 
- tips
tags:
- ruby
- gemfile
- bitbucket
---

### Any private gems in Gemfile that are hosted on bitbucket?

Add separate git_source for bitbucket provider to clean up your Gemfile. Here's an example:

{% highlight ruby %}
git_source(:bitbucket) { |repo| "git@bitbucket.org:#{repo}.git" }

gem 'my_private_gem', bitbucket: 'username/my_private_gem'
{% endhighlight %}
