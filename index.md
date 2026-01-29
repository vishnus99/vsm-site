---
layout: default
title: Home
---

# Welcome

I'm Vishnu Menon — Machine Learning Engineer. This is my blog where I write about ML, game dev, and side projects.

## Posts

{% for post in site.posts %}
- **[{{ post.title }}]({{ post.url }})** — {{ post.date | date: "%b %d, %Y" }}
{% endfor %}
