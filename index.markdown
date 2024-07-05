---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: Home
---

Welcome to my website, a central place where you can view everything I have been working on. 

My CV can be viewed [here](/assets/Downloadable/PaulBrown_CV%20-%20Google%20Docs.pdf)

I am a recent graduate of Bournemouth University with a First Class Degree in BSc (Hons) Games Design. I have been involved in a variety
of projects, from academic to commercial. I am looking for Technical Designer Roles or roles that require me to wear many different hats. I am
open to a lot of different types of projects and am not afraid of approaching a new challenge or genre. 

I'm still based in Bournemouth, but am open to relocation if the right role requires it. 

## Professional Projects

<ul class="post-list">
{% assign posts = site.ProfessionalProjects | sort: 'date' | reverse %}
  {% for post in posts %}
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

## Academic Projects
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


My personal projects and tutorials are another major part of my skillset expansion. I use them to experiment with the game engines I work with and to
communicate anything useful I have found to the wider community. I have even founded my own studio for my games. 

## Personal Projects
<ul class="post-list">
{% assign posts = site.PersonalProjects | sort: 'date' | reverse %}
  {% for post in posts %}
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

## Tutorials
<div>
<ul class="post-list">
{% assign posts = site.Tutorials | sort: 'date' | reverse %}
  {% for post in posts %}
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
</div>