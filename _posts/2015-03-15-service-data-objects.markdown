---
layout: post
title: "Service Data Object Pattern"
excerpt: Service Data Objects (SDO) simplified
modified: 2015-03-15T23:05:32+00:00
categories: PHP
tags: [PHP,SDO,Service,Data,Object,Patterns]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2015-03-15T23:05:32+00:00
redirect_to: http://zf4.biz/blog/service-data-objects
---

Service Data Objects (SDO) have a [long history](http://en.wikipedia.org/wiki/Service_Data_Objects), 
particularly in the Java world.  [IBM](http://www.ibm.com/developerworks/library/j-sdo/) 
and BEA first came up with the idea and in today's API driven world, they retain currency.

Implementation in the PHP world has been at best patchy, and at worst limited.  There
does exist a [PECL SDO package](http://php.net/sdo) which hasn't been maintained
since 2008 and is geared towards using XML as the data transport.  XML, by the way,
was the first intended transport format for SDOs, IBM being a huge proponent of the
technology.

Those that know me well, know I like XML and will use it where it makes sense to
do so.  However, today's version of the real world is a mess of API calls to third 
party API endpoints. We have JSON, json-rpc, xml-rpc, REST, SOAP, AJAX - you name it, we've 
got it.  As a developer of systems, I don't actually don't give a 
*expletive deleted*. What concerns me is that when some upstream brat decides
to *update* their api, my system is easily fixable.

Put simply, a SDO separates your internalised conceptual version of reality from
how it is in the real world. It builds on that venerable pattern, The Facade. 
When you dissect a service call, whether it be to a file system, a REST API etc., 
it boils down to three things:

- Can I get at the endpoint?
- Can I validate the incoming data?
- Can I map it to something I can use?

*Grandma sucking eggs warning* Facades enable us to pretend something is what it
isn't. SDOs do the same thing but in specific way.

SDOs, or at least the variation of them that I have used for a long time, comprise
of four components.  The UML is:

<img src="/images/SDO-Pattern.jpg"/>

## Transport interface

The Transport is responsible for fetching and sending data from/to the remote
endpoint.  This can of course utilise any technology you want; http, file, memcache,
reddis, mongodb, mysql etc.  It only knows how to fetch data and send it.

The read() method does a read of the remote endpoint and returns the response in
its external format.  The write() method takes data in the required external format
and writes it out to the endpoint.

## Mapper interface

The Mapper is responsible for converting to/from the data's external format from/into
the internal representation required by the application.  Mappers create internal 
format classes by themselves or employ builders to do it on their behalf.

The mapIn() method maps external data to its internal format and returns it.  The
method assumes that the external data format is valid.

The mapOut() method takes the internal format data and returns the external format of
the data.

## Validator interface

The Validator has a single responsibility, 
    
- to ensure that the incoming, external format data is valid and conforms to the requirements of 
the system. 

If your external endpoint returns XML, this is relatively trivial: use an XSD or DTD to
validate.  If your endpoint returns some other format, then your validator needs
to do some work.  

However, consider this:  Do you actually need to validate every
time you fetch data?  If the validator fails, the SDO will expect to do nothing, 
and probably throw some sort of exception and your application will have to process
that.  In the real world, that means your application has become a victim of
aforesaid upstream brat changing the API without telling you.  A much more pragmatic
approach is to have validation operative in your test integration environment and
have those tests running on a regular (daily or hourly) basis.  This gives you forewarning.

In production, you'd use a passthru validator (i.e. simply returns true.) If you
use dependency injection, it becomes a trivial job to configure your running 
application for dev, test, staging and production environments.  The other benefit
of this approach is that you can cut out, at least for production, what can be
relatively expensive validation operations.

## SDO interface

The SDO brings it all together.

The fetch() method simply does:

<pre>
    external = this.Transport.read()
    if (this.Validator.isValid(external) {
        this.data = this.Mapper.mapIn(external)
    }
    return this
</pre>

Assuming the fetch has worked, grab your internal data structure with

<pre>
    myData = sdo.getData()
</pre>

Many languages allow you to access the internal format data as an attribute of
the sdo itself. If yours does, then use it.

The send() method does:

<pre>
    this.Transport.send(this.Mapper.mapOut(this.data))
    return this
</pre>

The SDO supports a setData() method as a convenience, allowing client software
to set the internal format data object directly.

You can find a PHP implementation of the SDO Pattern at [chippyash/SDO-Pattern
](https://github.com/chippyash/SDO-Pattern).