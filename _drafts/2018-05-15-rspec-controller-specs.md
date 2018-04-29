---
layout: post
date: 2018-05-13 12:36
title: "rspec controller specs"
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

Unstubbed network requests in specs come in all sort of issues:

failure_message = "Failed due to: assigns(:your_model).errors.full_messages}
expect(response).to redirect_to(success_path), failure_message

aggregate_failures "testing response" do
  #expect(response.status).to eq(200)
  #expect(response.headers).to include("Content-Type" => "application/json")
  #expect(response.body).to eq('{"message":"Success"}')
end
