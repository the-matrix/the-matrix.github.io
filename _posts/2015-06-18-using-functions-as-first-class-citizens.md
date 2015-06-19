---
layout: post
title: "Using function as first class citizens in PHP"
excerpt: Function as first class for PHP
modified: 2015-06-19T23:05:32+00:00
categories: PHP
tags: [PHP,Functional]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2015-06-18T23:05:32+00:00
---

Consider this:
<pre>
def findFirst[A] (as: Array[A], p: A => Boolean): Int = {}
</pre>

What does that mean?  Well, It's Scala is one hint, but more informative is

We have just declared a function 

- that can take an ambiguous type as its first input parameter,as long as it is
an array of them
- takes as its second parameter a function that takes the ambiguous type as its
parameter and returns a boolean
- will return an Int as its output 

BTW the `A` in the above definition is considered as a `placeholder`, meaning

I don't care what you give me as `A` as long as you are consistent.

It could have been written as 
<pre>
def findFirst[Foo] (as: Array[Foo], p: Foo => Boolean): Int = {}
</pre>


In PHP:

<pre>
function findFirst (array $as, \Closure $p) { ... }
</pre>

Oops, what about the return type? Bummer, best you can do is type hint in the
documentation, although I hear that PHP 7 may be doing something about that.
So for now:

<pre>
/**
 * @param array $as Whatever I want to describe this as, it's only a hint when
 *  all said and done
 * @param Closure $p Any old function will do!
 *
 * @return Integer
 */
function findFirst (array $as, \Closure $p) { ... }
</pre>

So far so bad; No return type specified, no signature for the Closure. Return type
I can sort of live with, because it can be hinted at in the docblock, but I'm blowed
If I know what the rest of it is doing (except of course I am writing this, but 
I hope you see my point?)

Can we get round these limitations? Of course we can; Let us define another Class
that we descend from, another Trait we can `use`. Let's not.

