---
layout: page
title: University Projects
permalink: /University/
---
<ul>
  {% for post in site.University %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>