---
layout: post
date: 2021-06-18 16:00
title:  "org-babel snippet for Rails authentication"
category:
- tips
tags:
- org-babel
- babel-http
- ruby-on-rails
- devise
---

org-babel snippet for Cookie-based authentication in Rails/devise.

```
#+NAME: admin-cookie
#+HEADER: :var email="admin@example.com"
#+HEADER: :var password="secret"
#+BEGIN_SRC http :get-header "Set-Cookie"
POST ${baseUrl}/users/sign_in
Content-Type: application/json

{
  "user": {
    "email": "${email}",
    "password": "${password}"
  }
}
#+END_SRC
```

Once you get that cookie string, you're free to perform any other org-babel http requests as signed in user.

```
#+HEADER: :var admin-cookie=admin-cookie
#+BEGIN_SRC http
GET ${baseUrl}/leaderbit-entries
Cookie: ${admin-cookie}
#+END_SRC
```
