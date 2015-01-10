---
layout: page
title: The Big Boys Curry Book
excerpt: "Big Boys Curry Book reborn"
tags: ["bbcb", "curry", "india"]
modified: 2015-01-08T08:29:07.573882-04:00
comments: false
image:
  feature: the-matrix-slim.jpg
---

<div class="home">

<p>Many years ago, when I was just a small boy, ....</p>

<p>Nah, that's rubbish!</p>

<p>My first PHP web site was called 'The Big Boys Curry Book' and was launched too
many years ago to remember.  It's been defunct for a few years now so I thought
it might be fun to sort of relaunch it here as an archive of the recipes.</p>

<p>You can find the <a href="/big-boys-curry-book/glossary/">glossary here</a></p>

<h3>The recipes</h3>
  <ul class="post-list">
    {% assign sorted_pages = (site.categories.big-boys-curry-book | sort: 'title') %}
    {% for post in sorted_pages %}
      <li>
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
      </li>
    {% endfor %}
  </ul>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | prepend: site.baseurl }}">via RSS</a></p>

</div>

