---
layout: default
title: Solo Projects
permalink: /SoloProjects/
---

<ul>
  {% for post in site.SoloProjects %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>