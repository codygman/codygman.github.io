---
layout: page
title: Cody Goodman's Blog
tagline: Supporting tagline not found
---
{% include JB/setup %}
# Posts Cody has made
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## To-Do
Make homepage a little friendlier!

