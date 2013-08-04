---
layout: post
title: "Hello, world!"
date: 2013-08-04 8:30 AM
---

First post. Hi.

``` common lisp
(def fib (-> (n)
  (if (< n 2) n
    (+ (fib (- n 1)) (fib (- n 2))))))
```
