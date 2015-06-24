---
layout: post
title: "A Functional For Comprehension"
excerpt: Making PHP code more readable
modified: 2015-06-24T21:05:32+00:00
categories: PHP
tags: [PHP,Functional,For]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2015-06-24T21:05:32+00:00
---

As you get more experienced with a programming language, you learn its beauty spots,
side alleys and cess pools.  As a developer, I'm always looking to write less code
on a day to day basis, and PHP can be pretty verbose on a bad day.

One thing that arose from my recent excursions into Scala was the `For Comprehension`.  
Scala has a neat way of grouping up a series of dependent steps, in particular
if you look at example 2 of[this explanation](http://docs.scala-lang.org/tutorials/FAQ/yield.html), 
then here is room for a little expansion of the venerable FOR loop in PHP.

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

In essence, this construct is a `builder pattern`, and it would be much nicer to
write something like:

<pre>
$drink = FFor::create()
            ->ground((){$this->grind(new CoffeeBeans("Arabica"));})
            ->water((){$this->heatWater(new Water(25));})
            ->espresso(($ground, $water){$this->brew($ground, $water);})
            ->foam((){$this->frothMilk(new Milk("skinny"));})
            ->combine(($espresso, $foam){$this->combine($espresso, $foam);})
            ->release('combine');
</pre>

i.e. without the `function` prefix, and the assumption that the last statement
is the returning value.  But hey ho, might have to wait for that.  In the mean
time, if you think you are writing too much/incomprehensible code then you can
find the FFor comprehension in my working repo.  At the moment, I'm trying to
figure out how to fire off each of the clauses as asynchronous items.  I may fail 
at this, but in any event the FFor construct will make it into a Packagist
format some time soon.