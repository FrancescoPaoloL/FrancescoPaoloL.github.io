---
layout: default
title: Home - Francesco Paolo Lezza
---
<ul>
  {% assign about_post = site.posts | where_exp: "post", "post.url contains 'aboutme'" | first %}
  {% if about_post %}
    <li>
      <a href="{{ about_post.url | relative_url }}">{{ about_post.title }}</a>
    </li>
  {% endif %}

  {% assign other_posts = site.posts | where_exp: "post", "post.url != about_post.url" %}
  {% assign posts_by_year = other_posts | group_by_exp: "post", "post.date | date: '%Y'" %}

  {% for year_group in posts_by_year %}
    <li>
      <strong>{{ year_group.name }}</strong>
      <ul>
        {% for post in year_group.items %}
          <li>
            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
            <small> - {{ post.date | date: "%B %-d, %Y" }}</small>
          </li>
        {% endfor %}
      </ul>
    </li>
  {% endfor %}
</ul>
