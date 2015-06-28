---
layout: post
title: "A Functional For Comprehension"
excerpt: Making PHP code more readable
modified: 2015-06-25T21:05:32+00:00
categories: PHP
tags: [PHP,Functional,For]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2015-06-24T21:05:32+00:00
---

As you get more experienced with a programming language, you learn its beauty spots,
side alleys and cess pools.  As a developer, I'm always looking to write code that
explains itself better, so I can avoid writing comments (lazy but true!). 
PHP can be pretty abstruse on a bad day.

One thing that arose from my recent excursions into Scala was the `For Comprehension`.  
Scala has a neat way of grouping up a series of dependent steps, in particular
if you look at example 2 of [this explanation](http://docs.scala-lang.org/tutorials/FAQ/yield.html), 
then there is room for a little expansion of the venerable FOR loop in PHP.

By way of example:

<pre>
$drink = FFor::create()
            ->ground(function(){return $this->grind(new CoffeeBeans("Arabica"));})
            ->water(function(){return $this->heatWater(new Water(25));})
            ->espresso(function($ground, $water){return $this->brew($ground, $water);})
            ->foam(function(){return $this->frothMilk(new Milk("skinny"));})
            ->combine(function($espresso, $foam){return $this->combine($espresso, $foam);})
            ->release('combine');
</pre>


In classic PHP, this would become something like:

<pre>
//first cut
$ground = $this->grind(new CoffeeBeans('Arabica');
$water = $this->heatWater(new Water(25));
$espresso = $this->brew($ground, $water);
$foam = $this->frothMilk(new Milk("skinny"));
$drink = $this->combine($espresso, $foam);

//refactor
$drink = $this->combine(
    $this->brew(
        $this->grind(new CoffeeBeans('Arabica'),
        $this->heatWater(new Water(25))
    ),
    $this->frothMilk(new Milk("skinny"))
);
</pre>

Neither of which are really satisfactory. The first cut has too many variables
to juggle, the refactor loses the pitfalls of the first cut but introduces a kind
of `wadda f is going on here` response.  Both these shortcomings are, in my view,
alleviated by the FFor comprehension, which although slightly wordier due to the
limitation of the PHP syntax for Closures, make reading the intent easier.

In essence, this construct is a [`builder pattern`](https://github.com/chippyash/Builder-Pattern).

And it would be much nicer to write something like:

<pre>
$drink = FFor()
    ->ground((){$this->grind(CoffeeBeans("Arabica"));})
    ->water((){$this->heatWater(Water(25));})
    ->espresso(($ground, $water){$this->brew($ground, $water);})
    ->foam((){$this->frothMilk(Milk("skinny"));})
    ->combine(($espresso, $foam){$this->combine($espresso, $foam);})
    ->release('combine');
</pre>

i.e. without the `function` and `new` prefixes, a class called statically will produce an
object and the assumption that the last statement
is the returning value.  But hey ho, might have to wait for that.  In the mean
time, if you think you are writing too much/incomprehensible code then you can
find the FFor comprehension in my [working repo](https://github.com/chippyash/working/blob/master/src/chippyash/Funclang/FFor.php).
At the moment, I'm trying to figure out how to fire off each of the clauses as 
asynchronous items.  I may fail  at this, but in any event the FFor construct 
will make it into a Packagist format some time soon.

## Addendum

Having had a bit of time to think about this, it really is a form of a builder pattern.
With this in mind, I've renamed it, modified it and made it a bit more generic.
It is still synchronous, but the async stuff is under way.  You can find the soon
to be `packaged for packagist` library in my [Assembler Library](https://github.com/chippyash/Assembly-Builder) 

Run the examples/OneManCoffeeShop.php script to see it in action.

Check out this blog later for an article that explains how it works, there are some
neat tricks involved, or simply read the source code ;-)

Also check out the test code, it will (hopefully) give you insight into how it is
all used before I get the chance to complete the documentation.
