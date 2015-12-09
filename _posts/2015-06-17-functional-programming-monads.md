---
layout: post
title: "Functional programming with Monads in PHP"
excerpt: Monadic objects for PHP
modified: 2015-06-17T23:05:32+00:00
categories: PHP
tags: [PHP,Functional,Monad]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2015-06-17T23:05:32+00:00
redirect_to: http://zf4.biz/blog/functional-programming-monads
---

I've recently started to seriously learn [Scala](http://www.scala-lang.org/) (so
we might get some articles about that in this blog.) The prime motivator is that
that I've long held the belief that functional programming holds some lessons
for us PHP devs that we are failing to learn.  Scala, being a cross between an
Object Oriented language and a pure Functional language, also provides a vehicle
for learning these concepts that provides a relatively easy ride for poor PHPers
like me!

Very early on in my learning I came across the [Monad](https://en.wikipedia.org/wiki/Monad_\(functional_programming\))
and decided it would be cool to implement one in PHP, mainly as a way of learning
what they are and how they work.  You can find the product of that at [Packagist](https://packagist.org/packages/chippyash/monad)

## So what is a Monad?

A Monad has three things (according to my understanding of it):

- a value (which may be no value at all, a simple type, an object or a function)
- a method of getting its value, often referred to as return()
- a way of binding (or using) the value into some function, often referred to as 
bind(), the return value of which is another Monad, often but not always of the 
same type as the donor Monad.

In PHP, some of this clashes with reserved words, so the basic Monadic interface
can be defined as

<pre>
interface Monadic {
    value():mixed
    bind(Closure:function):Monadic
}
</pre>

And there is a rule for Monads:

- a Monad is immutable

It transpires that using this simple interface and obeying the one rule can lead
to a powerful programming tool.  Take this function to flatten the value (i.e.
return the native PHP type contained inside a collection (Monad\Collection):

<pre>
    /**
     * Return value of Monad as a base type.
     * If value === \Closure, will evaluate the function and return it's value
     * If value === Monadic, will recurse
     *
     * @return mixed
     */
    public function flatten()
    {
        $ret = [];
        foreach ($this->value as $key => $value)
        {
            $ret[$key] = Match::on($value)
                ->Closure(function($v){return $v();})
                ->Monad_Monadic(function($v){return $v->flatten();})
                ->any()
                ->flatten();
        }
        return $ret;
    }
</pre>

This uses a monadic Match method (Monad\Match) as an all powerful functional
case statement:

`Match::on()` : Static factory method to set the value that we want to match against. 
Returns a Match object

`->Closure()`: tries to match the value as being a Closure function. Returns a Match object

`->Monad_Monadic`: tries to match the value as being a Monad\Monadic implementing object.
Returns a Match object

`->any()`: will match against anything, bit like the default clause in a 
`switch statement`. Returns a Match object

`->flatten`: return the value being harboured by the Monad as a PHP Native or non
Monadic class. Another way to get the value();

The `Closure` and `Monad_Monadic` methods are virtual methods made available
through the use of the PHP magic `__call()` method.  Basically, you can match
against anything!

To write the above in *straight* PHP would give something like this:

<pre>
public function flatten()
{
    $ret = [];
    foreach ($this->value as $key => $value) {
        if (is_object($value) && $value instanceof Monad\Monadic) {
            $thisVal = $value->flatten();
        } elseif(is_callable($value) {
            $thisVal = $value();
        } else {
            $thisVal = $value;
        }
        
        $ret[$key] = $thisVal;
    }

    return $ret;
}
</pre>

Now, I know which I prefer for simple readability!

You can find out more about the Monads in the documentation for the library:

[https://github.com/chippyash/Monad](https://github.com/chippyash/Monad)