---
layout: page
title: Professional Projects
permalink: /ProfessionalProjects/
regenerate: true
---

These are the projects that I have been involved with since leaving University. 

<ul class="post-list">
{% assign posts = site.ProfessionalProjects | sort: 'date' | reverse %}
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
