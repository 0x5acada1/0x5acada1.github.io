---
layout: default
title: Home
---
<ul>
    <li>
      <a href="/ctf/">CTF</a>
    </li>
</ul>

<hr>
## 最近の投稿
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
