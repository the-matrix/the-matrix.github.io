---
layout: post
title: "Creating an XSD Server Using Slim"
modified:
categories: [PHP,XML]
excerpt: Creating a super simple server to host your XML Definitions
tags: ["XML","XSD","server"]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2014-12-22T23:29:56+00:00
redirect_to: http://zf4.biz/blog/creating-an-xsd-server-using-slim
---

Following the work I did yesterday to create Zend ACL definitions from XML files,
it followed that I needed to host the XSD in a public place.  The Slim Framework
is ideal for these jobs, so here it is:

{% highlight PHP %}

    use Slim\Slim;

    $app = new Slim();
    $app->xsdDir = realpath(__DIR__ . '/..') . '/xsd';

    /**
     * Define routes
     */
    $app->get('/schema/:name',
            function($name) use ($app) {
                $file="{$app->xsdDir}/{$name}.xsd";
                if (!file_exists($file)) {
                    $app->halt("{$name} does not exist");
                }
                $app->response->setBody(file_get_contents($file));
                $app->response->headers->set("Content-type", "application/octet-stream");
            })->name('schema');

    $app->run();
{% endhighlight %}

Now this is very raw, and now doubt can benefit from some tarting up, getting a
nice homepage display etc, but in essence it works.  Basically, store your XSD
files under the public root in the xsd directory and then call the site with
/schema/name e.g.

<code>http://schema.zf4.biz/schema/zendacl</code>



