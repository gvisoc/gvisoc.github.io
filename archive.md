---
layout: page
permalink: /archive/
title: Browse All Posts By Date
---

<p>As my hosting is rather limited in pre-processing options for Jekyll, for now this is the best I can get for you to be able to check previous posts by date.</p>
{% assign sortedPosts = site.posts | reversed: 'date' %}
<ul>
{% for post in sortedPosts %}
{%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
    <li>{{ post.date | date: date_format }}:
    <a href="{{ post.url | absolute_url }}">
      {{ post.title }}
    </a></li>
{% endfor %}
</ul>