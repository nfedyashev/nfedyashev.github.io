---
layout: post
date: 2018-04-14 12:36
title: "mitm as logging proxy for webmock"
description: Basic information about mitmproxy.
comments: false
category: 
- development 
tags:
- webmock
- tdd
- mitm
---


### Usage

```
man mitmdump
       -d, --detail
              Increase flow detail display level.  Can be passed multiple times.
```

you should rather use -ddd parameter(yes, 3 d) for better debugging if you have long JSON responses. Same output


<!--more-->

Set in custom spec runner as ENV variables:
```
http_proxy=http://0.0.0.0:8080
https_proxy=http://0.0.0.0:8080
```

Install and activate new certificate:
```
cd ~/.mitmproxy/
openssl x509 -in mitmproxy-ca.pem  -inform PEM -out mitm.crt
sudo mkdir /usr/share/ca-certificates/extra
sudo cp mitm.crt /usr/share/ca-certificates/extra
sudo dpkg-reconfigure ca-certificates
```

# Add the following line to your spec helper:
WebMock.allow_net_connect!
#TODO - think about ways to extract it as another ENV var and conditionally check its presence in runner. It belongs to where http_proxy&https_proxy were set.

Run proxy server:
```
  ➜ mitmdump -dd 
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
```

```
            {
                "intent": "HouseCleanup", 
                "score": 0.00262348261
            }, 
            {
                "intent": "CaloryIntake", 
                "score": 0.00199532183
            }, 
            {
    (cut off)
```

127.0.0.1:40328: clientdisconnect





# How to use it

Run your specs as usual(without running mitmproxy)
choose one spec that fails due to *Errno::ECONNREFUSED: Failed to open TCP connection to 0.0.0.0:8080*
this is an indicator that there is a request that hasn't been stubbed.

Turn on mitmproxy(mitmdump), re-run spec, copy request output result, add it to your stub_request call and you're done.

Usually you don't need to run mitmproxy in permanent mode because
1) in most cases all you care about is that last requests in the logs
2) if it stays up you may miss other unstubbed requests.
