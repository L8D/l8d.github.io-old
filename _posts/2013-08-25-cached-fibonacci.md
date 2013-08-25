---
layout: post
title: "Cached Fibonacci"
date: 2013-08-25 2:00 PM
---

Basic and lazy fibonacci.

```coffeescript
fib = (n) ->
  return n if n < 2
  fib(n - 1) + fib n - 2
```

Fibonacci that uses a cache. Much faster.

```coffeescript
fib = (n, c=[0, 1]) ->
  unless c[n]?
    c[n] = fib(n - 1, c) + fib n - 2, c
  c[n]
```

Even though previous one is really fast, it creates seperate caches down the tree.
So it's nothing near it's proper potential.

To use a single and shared cache between every call, we either need to use a class or global variable. Since there isn't a `static`-like keyword anywhere in JavaScript.

Using a class:

```coffeescript
class Fib
  constructor: ->
    @cache = [0, 1]

  find: (n) ->
    unless @c[n]?
      @c[n] = fib(n - 1, c) + fib n - 2, c
    @c[n]
```

Using a global variable:

```coffeescript
window.fibCache = [0, 1]
fib = (n) ->
  unless fibCache[n]?
    fibCache[n] = fib(n - 1, c) + fib n - 2, c
  fibCache[n]
```

But still, these aren't nearly as fast as they could be.
Here's some similar looking C code:

```c
int fib(int n, int *c) {
  if(!c[n] && n != 0) {
    c[n] = fib(n - 1, c) + fib(n - 2, c);
  }
  return c[n];
}
```

Looks good right? **WRONG**.
There's nothing that is setting up an array for that, so we would need another function that creates an array appropriately sized and such.

```c
int fibc(int n) {
  int *c = calloc((n + 1), sizeof *c);

  c[1] = 1;

  return fib(n, c);
}
```

In this case, we end up using `calloc` to create an array of zeros just long enough for our needed number.
Then we set the second element in the array to 1.
We don't need to set the first to 0, because it would already be 0.

Notice there was no shared-global-whatever value needed.
That's because since we're just passing a pointer to the same location, all the writing that occurs stays in the same place.
Though of course, I could try to use a static variable in the fib function to avoid having a seperate function to set it up.
But then I would have to do some sort of checking wether or not the cache variable has been initialized properly for every function call. Not acceptable.

Since we're going to be dealing with huge numbers when calculating fibonacci, we need to use `unsigned long long`s.

Here is the resulting code.

```c
typedef unsigned long long big;

big fib(big n, big *c) {
  if(!c[n] && n != 0) {
    c[n] = fib(n - 1, c) + fib(n - 2, c);
  }
  return c[n];
}

big fibc(int n) {
  big *c = calloc((n + 1), sizeof *c);

  c[1] = 1;

  return fib(n, c);
}
```

It's *really* fast.

```c
fibc(100); // 3736710778780434371
```
