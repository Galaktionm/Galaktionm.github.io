---
layout: page
title: Posts
permalink: /posts/
---

{% for post in site.posts %}
    <div>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </div>
{% endfor %}

