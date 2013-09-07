---
layout: post
title: "Don't use CoffeeScript"
date: 2013-09-06 7:16 PM
---

The past few days I noticed a few things about CoffeeScript, and how it can be
a big performance issue in the long term.

I'm going start out by saying that CoffeeScript is a great language if you know
what you're doing. Also, I'm not going to be talking about why the syntax can be
unreadable at times.

CoffeeScript is convenient for developers, and not for the engines.

Something that I have to do regularly when working on my codebases is replace
all of those `x = (do_foo(y) for y in arr)` with
`x = arr.filter (y) -> do_foo(y)`. Do you know why? Hopefully you do.

```coffeescript
nums = [1, 2, 67, 43]

squared_nums = (number * number for number in nums)
```

Produces:

```javascript
var number, nums, squared_nums;

nums = [1, 2, 67, 43];

squareNums = (function() {
  var _i, _len, _results;
  _results = [];
  for (_i = 0, _len = nums.length; _i < _len; _i++) {
    number = nums[_i];
    _results.push(number * number);
  }
  return _results;
})();
```

Is all that really necessary? Especially when you know your code will be run on
V8, and supports ECMAScript 5. **No**, it isn't. Things like `number = nums[_i]`
and `_results.push(numer * number)` are unacceptable to me. Yes, I do have to
give some credits to putting the `number * number` directly into the push call,
but still, the code is totally inefficient. And they could've done
`squareNums.push` instead.

If you want to write reasonably efficient code in CoffeeScript, you need to do:

```coffeescript
nums = [1, 2, 67, 43]

squareNums = nums.filter (number) -> number * number
```

Which produces:

```javascript
var nums, squaredNums;

nums = [1, 2, 67, 43];

squaredNums = nums.filter(function(number) {
  return number * number;
});
```

Which, not only is it less character, lines of code, and extranous variables,
but it uses the built-in and V8-optimized `Array.prototype.filter` function.

I understand the reasonings for CoffeeScript trying to support all browsers, but
for a lot of the work I do, I don't need to support all browsers, or I'm running
node.js which I now is V8 and ECMAScript 5.

Additionally, if you want to prevent a ton of wasted efforts by the interpreter,
you have to put a `return` at the end of your functions. What boggles my mind
is why CoffeeScript hasn't made something like `-->` or some shortcut to prevent
this unnecessary return. For example, you might see something like this in your
code.

```coffeescript
printParsed = (vars) ->
  for i in vars
    console.log formatFunction i
```

You won't realize it at first, but CoffeeScript will setup a complicated set of
code to return the array of the results from calling console.log, which will
return an array of `undefined`.

```javascript
var printParsed;

printParsed = function(vars) {
  var i, _i, _len, _results;
  _results = [];
  for (_i = 0, _len = vars.length; _i < _len; _i++) {
    i = vars[_i];
    _results.push(console.log(formatFunction(i)));
  }
  return _results;
};
```

So, to properly do this, I need to write:

```coffeescript
printParsed = (vars) ->
  vars.forEach (i) ->
    console.log formatFunction i
    return
  return
```

And now I'm sure that CoffeeScript devs are cringing at this.

```javascript
var printParsed;

printParsed = function(vars) {
  vars.forEach(function(i) {
    console.log(formatFunction(i));
  });
};
```

And that's still not ideal. We should be able to do:

```javascript
var printParsed = function(vars) {
  for(var i = 0; i < vars.length; ++i) {
    console.log(formatFunction(vars[i]));
  }
}
```

We don't need to worry about caching the `vars.length`, since in V8, it *is* a
variable, not a getter, and is only changed on changes to the array(like with
`.push` and `.shift` methods).

There are a few more examples I could show, but I'm too lazy to go through them.

## CoffeeScript isn't *just* JavaScript.
