---
layout: post
title: "Simple Accounts and Currencies"
excerpt: Simple double entry book-keeping
modified: 2015-05-16T23:05:32+00:00
categories: PHP
tags: [PHP,Accountancy,Currency]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2015-05-16T23:05:32+00:00
---

Whilst full blown accounting systems are available, requiring a massive 
integration effort, some applications simply need to be able keep some form of 
internal account. 

The library is the direct descendant of something I wrote for a client many 
years ago to keep account of game points earned on a web site. Using the double 
entry accounting paradigm allowed the site owner to keep track of who had 
gathered points, and in which ways, whilst at the same time seeing what this 
meant to their business as game points translated into real world value for the 
customer by way of discounts and prizes.

To support it, there is also a Currency library providing a strong type 
implementation of an ISO-4217 Current Currency.

You can find the libraries on Packagist as normal

[Simple Accounts](https://packagist.org/packages/chippyash/simple-accounts)

[Currencies](https://packagist.org/packages/chippyash/currency)
