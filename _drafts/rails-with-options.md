---
layout: post
date: 2018-05-13 12:36
title: "RSpec graceful failure"
comments: true
category: 
- testing
tags:
- rspec
- tdd
- testing
- rails
- ruby-on-rails
---

Rarely used #with_options which could come really handy for DRYing your
  with_options allow_nil: false, allow_blank: false do |assoc|
    validates :claim_type, inclusion: CLAIM_TYPES
    validates :month, inclusion: MONTH_NAMES
    validates :year, inclusion: YEARS
  end

rails attributes api type casting
