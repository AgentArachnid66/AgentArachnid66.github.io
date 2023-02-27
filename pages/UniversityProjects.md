---
layout: page
title: University Projects
permalink: /University/
---
<ul class="post-list">
  {% for post in site.University %}
    <li>
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
            <img src="{{ post.thumbnail }}" />
          </a>
        </h3>
        {%- if site.show_excerpts -%}
          {{ post.excerpt }}
        {%- endif -%}
      </li>
  {% endfor %}
</ul>