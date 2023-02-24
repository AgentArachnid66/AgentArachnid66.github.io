---
layout: page
title: University Projects
permalink: /University/
---
<ul class="post-list">
  {% for post in site.University %}
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