---
layout: post
date: 2018-04-14 12:36
title: "mitmproxy as logging proxy for webmock"
comments: true
category: 
- testing
tags:
- webmock
- vcr
- tdd
- testing
- logging
- proxy
- mitm
---

Unstubbed network requests in specs come in all sort of issues:

* specs failing unpredictably due to connectivity issues
* significantly slower test suites
* hitting API rate limits on 3rd party services

So running specs in isolation should always be high in your priority list.

<!--more-->

### VCR

Many developers to solve this issue use <a href="#">VCR</a> to record live interaction and "replay" it back in specs.
I used it as well in a couple of projects with somewhat limited success.

Cons that I found in VCR:

* specs become a bit noisy with unrelated terminology of cassette handling
* specs doesn't clearly represent system under test because the most important info is hidden in recorded cassettes which are not in your actual spec files.
* changing the written response body in recorded VCR cassette manually <a href="https://stackoverflow.com/questions/21920259/how-to-edit-response-body-returned-by-vcr-gem">is not always easy</a> and itself is an anti-pattern in VCR approach.


### Webmock

Webmock allows us to operate on a lower level and gives us ability to update output more easily(and keep specs clean and concise).
But before stubbing network request you need to know the exact default response by third-party service that you need to replicate in your specs.

### mitmproxy to the rescue!

Here's the solution that I tried in several projects that worked great for me:
 
<a href="https://mitmproxy.org/">mitmproxy</a> is a free and open source interactive Man-in-the-middle HTTPS proxy.


You run your spec with the following system *ENV variables*: `http_proxy=http://0.0.0.0:8080 https_proxy=http://0.0.0.0:8080` so it forwards your network requests through mitmproxy running in debug mode.
All unstubbed requests would be displayed in logs like so:

{% highlight console line_numbers=false %}
➜ mitmdump -ddd
Proxy server listening at http://0.0.0.0:8080
127.0.0.1:40278: clientconnect
127.0.0.1:40278: 
    GET https://jsonplaceholder.typicode.com/posts/1
    Accept: */*
    Accept-Encoding: gzip, deflate
    User-Agent: rest-client/2.0.2 (linux-gnu x86_64) ruby/2.4.1p111
    Host: jsonplaceholder.typicode.com

 << 200 OK 202b
    Date: Tue, 20 Mar 2018 20:58:10 GMT
    Content-Type: application/json; charset=utf-8
    Transfer-Encoding: chunked
    Connection: keep-alive
    Set-Cookie: __cfduid=d1edbbdfad6dd9d7eff5a4f6efb2914c31521579490; expires=Wed, 20-Mar-19 20:58:10 GMT; path=/; domain=.typicode.com; HttpOnly
    X-Powered-By: Express
    Vary: Origin, Accept-Encoding
    Access-Control-Allow-Credentials: true
    Cache-Control: public, max-age=14400
    Pragma: no-cache
    Expires: Wed, 21 Mar 2018 00:58:10 GMT
    X-Content-Type-Options: nosniff
    Etag: W/"124-yiKdLzqO5gfBrJFrcdJ8Yq0LGnU"
    Via: 1.1 vegur
    CF-Cache-Status: HIT
    Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
    Server: cloudflare
    CF-RAY: 3feb1869cdb99cb3-AMS
    Content-Encoding: gzip

    {
        "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto", 
        "id": 1, 
        "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit", 
        "userId": 1
    }

127.0.0.1:40278: clientdisconnect
{% endhighlight %}

Just copy that response JSON and add it to your Webmock *stub_request* configuration.
