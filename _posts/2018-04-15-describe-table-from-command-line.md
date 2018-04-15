---
layout: post
date: 2018-03-15 12:36
title: "Describe table from command line"
description: Basic information about table describing
comments: true
category: 
- tips
tags:
- postgresql
- activerecord
- db-schema
---


### Usage


```
psql eth_farm_development -c  '\x' -c '\d+ books'
```

```
                                                           Table "public.books"
    Column     |            Type             |                     Modifiers                      | Storage  | Stats target | Description 
---------------+-----------------------------+----------------------------------------------------+----------+--------------+-------------
 id            | bigint                      | not null default nextval('books_id_seq'::regclass) | plain    |              | 
 price         | numeric(10,2)               |                                                    | main     |              | 
 supplier_type | character varying           |                                                    | extended |              | 
 supplier_id   | bigint                      |                                                    | plain    |              | 
 created_at    | timestamp without time zone | not null                                           | plain    |              | 
 updated_at    | timestamp without time zone | not null                                           | plain    |              | 
Indexes:
    "books_pkey" PRIMARY KEY, btree (id)
    "index_books_on_supplier_type_and_supplier_id" btree (supplier_type, supplier_id)

```
