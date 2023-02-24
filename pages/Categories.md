---
layout: page
title: Categories
permalink: /Categories/
---



{% assign thisCollection = site.University %}	
{% assign allCats =  thisCollection | map: 'categories' | join: ','  | split: ','  | group_by: category %}

{% capture category_array %}
  {% for eachCat in allCats %}
    {{ eachCat.name }}|
  {% endfor %}
{% endcapture %}


{% for eachCat in allCats %}
  {% for post in thisCollection %}
    
    {% if post.categories contains eachCat.name %}

      {% assign thisCat = eachCat.name | append: '|' %}

      {% if category_array contains thisCat %}
        <!-- Apply your code here to display the category list -->
      {% endif %}

      {% assign category_array = category_array | remove: thisCat %}
    {% endif %}

  {% endfor %}
{% endfor %}