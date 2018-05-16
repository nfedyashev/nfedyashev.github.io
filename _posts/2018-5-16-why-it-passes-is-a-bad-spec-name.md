---
layout: post
date: 2016-05-16 16:00
title:  "Why it \"passes\" is bad spec name"
category: 
- testing
tags:
- rspec
- tdd
- testing
- code-review
---

In this post I'll try to explain why you should avoid naming your RSpec scenarios like
 
 * <code>it "does something"</code>
 * <code>it "works"</code>
 * <code>it "passes"</code>
 * <code>it "fails"</code>
 * <code>it "does not fail"</code>
 
<!--more-->
 
 I saw such specs quite often and admittedly written some myself until I figured out how to fix this code smell.
 
### What exactly is wrong with such specs?
 
It is repetitive.

It is attempting to document the obvious.

It is like a [Filler](https://en.wikipedia.org/wiki/Filler_(linguistics)){:target="_blank"} word which doesn't provide value in specs.

### Why this pattern is so popular and widespread?

I guess it could be coming from the official rspec-core documentation which 1.5k+ instances of it "with some messages" examples and [just one without custom message](https://github.com/rspec/rspec-core/blob/master/features/mock_framework_integration/use_rspec.feature#L106){:target="_blank"}.
It could also be coming from other books and blog posts where each spec covers very specific scenario and contain some meaningful information in scenario declaration.
But your spec could be too simple or too self explanatory that it doesn't need to have its own name.

Another reason why I think it could be so wide spread is because `it` blocks(not `specify` or `example`) are the most popular writing RSpec specs. It is in fact [the primary API to define a code example](https://github.com/rspec/rspec-core/commit/3951d7c6cd4f519913b1bed74c78ae3ae331e3b5#diff-89a77987791b99dfc9d2e744919d0c42R89) accordingly to the source code of rspec-core.
When you attempt to describe the behaviour or your system starting with word `it` you always include words, some words, even if it's just <code>it "does something"</code>, <code>it "works"</code>.
 
### What's the alternative?
 
Use `example` instead of `it`!

 `example` in many cases better conveys the meaning of your specs because what you're writing are basically examples.
 
 `example` without custom explanation message doesn't look as bad as `it`.
 
{% highlight ruby linenos %}
example do
  expect(described_class.new('input1').process).to eq('value2')

  expect { described_class.new('invalid input').process }.to raise_error
end
{% endhighlight %}
