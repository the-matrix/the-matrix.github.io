---
layout: post
title: "Installing the Saxon/C PHP Extension"
excerpt: Enabling XSLT2 support for PHP applications
modified:
categories: PHP
tags: [PHP,XSLT,XML,Saxon,Saxonica]
image:
  feature: the-matrix-slim.jpg
comments: true
date: 2015-01-01T18:35:32+00:00
---

XSLT support for PHP is limited to [XSLT Version 1](http://php.net/manual/en/book.xsl.php).  
The standard has moved on and it appears that PHP is not about to catch up any 
time soon. You can get round this at the expense of a little bit of heavy 
lifting on your part.

[Saxonica](http://www.saxonica.com/), home of the Saxon family of XSLT and XQuery
processors, have by virtue of their Saxon/C library, provided a PHP extension
that can be used to give access to XSLT V2.  The basics of installation are provided
[here](http://www.saxonica.com/saxon-c/index.xml), but I found that I had to do 
a couple of extra things to get it up and running, so this post is about installing
on an Ubuntu 64 bit system (mine is Linux Mint 17) running PHP 5.5  

If it helps, then it works!  However, knowing from experience that everyone's
setup can be slightly different, please comment on this post if you find inconsistencies
etc. It would be helpful if you could note your OS, version, flavour etc.

**sudo to root now**.

## Install dependencies

This assumes you already have Apache installed (although technically, this isn't
necessary, you can run this stuff in cli mode.)

<pre>
    apt-get install openjdk-7-jdk build-essential php5-dev gcj-jdk
</pre>

## Install and build Saxon/C

Download  and unzip your platform [Saxon/C](http://www.saxonica.com/saxon-c/index.xml).

Run the installation and specify your target directory as /usr/lib:  NB. The 
official documentation is wrong, the installer will install to Saxonica/<version_dir>

Own it to root and allow read access to others:

<pre>
    chown -R root /usr/lib/Saxonica
    chmod -R u+r /usr/lib/Saxonica
</pre>

Link the dynamic saxon library so it can be found:

<pre>
    ln -s /usr/lib/Saxonica/Saxon-HEC0.3.1/libsaxon.so /usr/lib/libsaxon.so
</pre>

create the LD config file for Jetvm (I use nano, use whatever editor you are familiar with): 

<pre>
    nano /etc/ld.so.conf.d/jetvm.conf
</pre>

and add the contents:

<pre>
    #JetVM env path - required for Saxon
    /usr/lib/Saxonica/Saxon-HEC0.3.1/rt/lib/amd64
    /usr/lib/Saxonica/Saxon-HEC0.3.1/rt/lib/amd64/jetvm
</pre>

save the file and run ldconfig:

<pre>
    ldconfig
</pre>

Edit apache's environment:

<pre>
    nano /etc/apache2/envvars
</pre> 

and add to the end of the file:

<pre>
    ## Saxonica Saxon HE support
    export LD_LIBRARY_PATH=/usr/lib/Saxonica/Saxon-HEC0.3.1/rt/lib/amd64:/usr/lib/Saxonica/Saxon-HEC0.3.1/rt/lib/amd64/jetvm:$LD_LIBRARY_PATH
</pre>

save the file.

## Build and install the PHP extension

<pre>
    cd /usr/lib/Saxonica/Saxon-HEC0.3.1/Saxon-C-API/
    phpize
    ./configure --enable-saxon
    make
    make install
</pre>

Create a module conf file:

<pre>
    nano /etc/php5/mods-available/saxon.ini
</pre>

and add contents:

<pre>
    ; configuration for php Saxon HE module
    extension=saxon.so
</pre>

save the file.

Enable the module for PHP:

<pre>
    php5enmod saxon
</pre>

## Restart Apache

If you are running Apache then:

<pre>
    service apache2 restart
</pre>

## Check it is loaded

<pre>
    php -m | grep saxon -i
</pre>

should display

<pre>
    Saxon/C
</pre>

E&OE

Now it's time for a curry!
