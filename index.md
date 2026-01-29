---
layout: default
title: Home
---

<div class="home-hero">
  <h1 class="home-title">Vishnu Menon</h1>
  <p class="home-tagline">Machine Learning Engineer</p>
  <p class="home-intro">I write about ML, game dev, and side projects.</p>
</div>

{% assign latest = site.posts | first %}
{% if latest %}
<section class="home-latest">
  <h2>Latest post</h2>
  <article class="featured-post">
    <h3><a href="{{ latest.url | relative_url }}">{{ latest.title }}</a></h3>
    <p class="featured-post-meta">{{ latest.date | date: "%B %d, %Y" }}</p>
    <a href="{{ latest.url | relative_url }}" class="btn btn-primary">Read post</a>
  </article>
  <p><a href="{{ "/blog/" | relative_url }}">View all posts →</a></p>
</section>
{% endif %}
