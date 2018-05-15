---
layout: post
date: 2016-03-15 22:00
title:  "#caller_locations"
category: 
- docs
---

#[String] text e.g. "Foo"
def method1(var1)
  start, length = 1, 1
  locations = caller_locations(start, length)
  if locations.first.to_s.include?('partial_template_name')
