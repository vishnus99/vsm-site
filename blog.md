---
layout: page
title: Blog
permalink: /blog/
---

<article class="post-preview">
  <h2 class="post-preview-title"><a href="{{ '/mysterydungeon-gpt/' | relative_url }}">mysterydungeonGPT – An exploration in LLM text-to-map game level generation</a></h2>
  <p class="post-preview-meta">January 28, 2026 · machine-learning, game-development</p>
  <p><a href="{{ '/mysterydungeon-gpt/' | relative_url }}" class="post-preview-link">Read post →</a></p>
</article>

{% for post in site.posts %}
<article class="post-preview">
  <h2 class="post-preview-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
  <p class="post-preview-meta">{{ post.date | date: "%B %d, %Y" }} · {{ post.categories | join: ", " }}</p>
  <p><a href="{{ post.url | relative_url }}" class="post-preview-link">Read post →</a></p>
</article>
{% endfor %}
