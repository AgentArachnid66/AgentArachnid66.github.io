---
layout: default
title: SpyderWeb Studios
permalink: /SpyderWebStudios/
---

# This is where I update on any progress towards my personal projects, both Open Source and Proprietary

<ul class="post-list">
  {% for post in site.SoloProjects %}
    <li>
        <span class="post-meta">{{ post.date | date: "%-d %B %Y" }}</span>
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
          </a>
        </h3>
        {%- if site.show_excerpts -%}
          {{ post.excerpt }}
        {%- endif -%}
      </li>
  {% endfor %}
</ul>
