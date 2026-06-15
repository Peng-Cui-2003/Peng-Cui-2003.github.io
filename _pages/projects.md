---
permalink: /projects/
title: "Projects"
excerpt: "Research projects and notes"
author_profile: true
---

# Projects

{% assign project_posts = site.posts | sort: "date" | reverse %}

{% if project_posts.size > 0 %}
{% for post in project_posts %}
## [{{ post.title }}]({{ post.url | relative_url }})

<p class="small">{{ post.date | date: "%Y.%m.%d" }}</p>

{{ post.excerpt }}

{% endfor %}
{% else %}
No projects yet.
{% endif %}
