---
layout: default
title: Personal Projects
permalink: /PersonalProjects/
regenerate: true
---

# This is where I update on any progress towards my personal projects, both Open Source and Proprietary

<ul class="post-list">
{% assign posts = site.PersonalProjects | sort: 'date' | reverse %}
  {% for post in posts %}
    <li>
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
