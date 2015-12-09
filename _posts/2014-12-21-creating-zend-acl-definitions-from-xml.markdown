---
layout: post
title: "Creating Zend ACL Definitions From XML"
modified: 2015-01-07T19:45:00+00:00
categories: PHP
excerpt: How to create a Zend ACL definition from an XML file
tags: [builder,pattern,xml,zend,acl]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2014-12-21T18:57:15+00:00
redirect_to: http://zf4.biz/blog/creating-zend-acl-definitions-from-xml
---

In the current project I am working on, I use the Symfony Dependency Injection
library to implement DI. Specifically I use the XML variant as it is to my mind
the most performant way of doing things. Thus, having chosen XML as my configuration
paradigm, I want to stick to it.

I needed to be able to include an Access Control List (ACL) in the app, and my
first thoughts lay in the direction of the Zend ACL. In the Zend 2 version, it 
seemed to have lost a bit of functionality along the way, mainly in terms of how
to configure it externally.  Taking a peek around t'internet revealed that there
is a lack of XML support for the ACL.  I came across a blog post by [wolfulus](http://wolfulus.wordpress.com/2011/12/26/zend-framework-xml-based-acl-part-1/)
which lays the foundation, but is specifically aimed at using the ACL within a
Zend MVC implementation.

My project is using the Slim Framework, so it didn't quite fit the bill. A bit 
of mooching around Packagist and Github revealed [Jeremy Kendall's work](https://github.com/jeremykendall/slim-auth)
to implement Zend ACL into the Slim Framework. His implementation requires a
database backend, not something I want in my app.  But it did lead me to think.

So, first step was to abstract the problem.  Simply put, I need to be able to
create the ACL from an XML file in the most generic way possible. A night later 
and we have the answer - or at least I do ;-) 

Now you can share the result in
my [library Github account](https://github.com/chippyash/Zend-Acl-Xml-Builder).

<pre>
use chippyash\Zend\Acl\Xml\AclDirector;
use chippyash\Type\String\StringType;

$location = new StringType('/location/of/my/acl.xml');
$director = new AclDirector($location);
$acl = $director->build();
</pre>

Next step:

- See if I can bend/amend Jeremy's work to fit my requirement.

### Update:

I've now added the ability to import other ACL definitions and pass in ACL
definitions as text rather than as just filenames.  This allows you to build
complex ACLs from multiple parts and include the builder as part of a workflow
to create an ACL.  Also, the XML definition (XSD) is publicly available via a
URL.  See the docs for more detail.




