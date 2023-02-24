---
layout: page
title: Tutorials
permalink: /Tutorials/
---
<ul>
  {% for post in site.Tutorials %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>