---
permalink: /blog/
title: "Blog"
excerpt: "Notes and updates"
author_profile: true
---

# Blog

{% assign blog_posts = site.posts | sort: "date" | reverse %}

{% if blog_posts.size > 0 %}
{% for post in blog_posts %}
## [{{ post.title }}]({{ post.url | relative_url }})

<p class="small">{{ post.date | date: "%Y.%m.%d" }}</p>

{{ post.excerpt }}

{% endfor %}
{% else %}
No blog posts yet.
{% endif %}
