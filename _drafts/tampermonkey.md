---
layout: post
date: 2016-03-15 22:00
title:  "Tampermonkey"
category: 
- docs
---

quick log ins in development:

// ==UserScript==
// @name         New Userscript
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  try to take over the world!
// @author       You
// @match        http://localhost:3000/login
// @grant        none
// ==/UserScript==

(function() {
    'use strict';
    if (document.title != 'Your App') {
      return;
    }
    if (document.querySelectorAll('form[action="/login"]').length == 0){
        return;
    }

    document.getElementById('user_email').value = 'user1@example.com';
    document.getElementById('user_password').value = "foo";
    document.querySelector('input[name=commit]').click();
})();


TODO: any other use cases?
Pre-filling all forms with some dummy data in development?
