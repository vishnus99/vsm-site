---
layout: page
title: Blog
permalink: /blog/
---

# Blog

{% for post in site.posts %}
<article class="post-preview">
  <h2 class="post-preview-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
  <p class="post-preview-meta">{{ post.date | date: "%B %d, %Y" }} · {{ post.categories | join: ", " }}</p>
  <p><a href="{{ post.url | relative_url }}" class="post-preview-link">Read post →</a></p>
</article>
{% endfor %}
