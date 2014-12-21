---
layout: post
title: "Builder Pattern Gets Closures"
modified: 014-12-21T12==19:35:07+00:00
categories: PHP
excerpt: New feature release for Builder Pattern
tags: [builder, pattern, closures]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2014-12-21T12:25:07+00:00
---

In a small feature change to the Builder Pattern library, I've added the ability
for a build parameter to be defined as a closure. e.g. 

<pre>
    protected function setBuildItems()
    {
        $this->buildItems = [
            'name' => '',
            'createdate' => new \DateTime(),
            'account' => new AccountBuilder(),
            'exportName => function(){return 'BuilderPattern!';}
        ];
    }
</pre>

This adds a greater degree of flexibility to the build mechanism. The 
chippyash\Zend\Acl XML builder makes of this.

- [Builder Pattern](https://github.com/chippyash/Builder-Pattern)
- [Zend ACL from XML](https://github.com/chippyash/Zend-Acl-Xml-Builder)
 