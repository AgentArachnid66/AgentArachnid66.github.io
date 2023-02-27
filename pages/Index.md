---
layout: page
title: Index Page
permalink: /Index/
---

Here is a master list of all Posts on the Website

{% for collection in site.collections%}
{% if collection.label == 'posts' %}
    {% continue %}
  {% endif %}
  <h2>Items from {{ collection.label }}  </h2>
  <ul class="post-list">
    {% for item in site[collection.label] %}
      <li><a href="{{ item.url }}">{{ item.title }}</a></li>
<img src="{{ item.thumbnail }}" />
    {% endfor %}
  </ul>
{% endfor %}

