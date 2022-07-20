---
layout: default
title: Home
---
## CTF

<ul>
    <li>
      <a href="/ctf/writeups/">Writeups</a>
    </li>
    <li>
      <a href="/ctf/cheatsheets/">Cheatsheets</a>
    </li>
</ul>
---

## Recent posts

<ul>
  {% for post in site.posts limit:5 %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

{% include footer.html %}
