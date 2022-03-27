---
layout: default
title: Home
---
## CTF
<ul>
    <li>
      <a href="/ctf/writeups/">WriteUps</a>
    </li>
    <li>
      <a href="/ctf/cheatsheets/">CheatSheets</a>
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
<hr>
## All Categories
<div id="archives">
{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>

    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name | slugize }}"></a>
  </div>
{% endfor %}
</div>

