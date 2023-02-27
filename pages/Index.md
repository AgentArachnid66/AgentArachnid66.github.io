---
layout: page
title: Index
permalink: /Index/
---

Here is a master list of all Posts on the Website

{% for collection in site.collections%}
{% if collection.label == 'posts' %}
    {% continue %}
  {% endif %}
  <h2>{{ collection.label }}</h2>
  <ul class="post-list">
    {% for item in site[collection.label] %}
      <li> <a class="post-link" href="{{ item.url | relative_url }}">
            {{ item.title | escape }}
            <img src="{{ item.thumbnail }}" />
          </a></li>
    {% endfor %}
  </ul>
{% endfor %}

