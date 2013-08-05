---
layout: post
title: "Hipster vs Vintage"
date: 2013-08-04 11:30 PM
published: false
---

In today's modern languages we have hipster and vintage syntax.
Let me give you an example of comments:

### Hipster Comments
{% highlight ruby %}
# This is a single-line comment in languages like:
# CoffeeScript, Python, Perl, Awk, Joy, Julia, R, *sh
{% endhighlight %}
### Vintage Comments
{% highlight javascript %}
// This is a single-line comment in languages like:
// C, Java, JavaScript, Go, C#, Falcon, Haxe, Obj-C, Scala
{% endhighlight %}

### Hipster Blocks
Ruby
{% highlight ruby %}
def foo(args)
  args.each do |arg|
    puts arg
  end
end
{% endhighlight %}
Python
{% highlight python %}
def foo(args):
  for arg in args:
    print(arg)
{% endhighlight %}
CoffeeScript
{% highlight coffeescript %}
foo = (args) ->
  for arg in args
    console.log arg
{% endhighlight %}

### Vintage Blocks
{% highlight javascript %}
{
  foo();
}
{% endhighlight %}
