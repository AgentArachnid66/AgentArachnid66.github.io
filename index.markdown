---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: Home
---

Welcome to my website, a central place where you can view everything I have been working on. 

My CV can be viewed [here](/assets/Downloadable/PaulBrown_CV%20-%20Google%20Docs.pdf)


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


