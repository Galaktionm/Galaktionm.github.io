---
layout: page
title: Posts
permalink: /posts/
---

<div>
{% for post in site.posts %}
      <div>
      <a href="{{ post.url }}"> {{ post.title }} </a>
      </div>
{% endfor %}
</div>

