---
layout: page
title: Home
---

<br>
<p class="message">This is the personal blog of robmsmt. To contact me please visit <a href="about">about</a><br><br>


---

<ul>
  {% for post in site.posts %}
    <li>
      {{ post.date | date_to_string }} &raquo; <a href="{{ post.url }}">{{ post.title }}</a>
        <p><i>{{ post.excerpt | strip_html | truncatewords:75}}</i></p>
      </li>
  {% endfor %}
</ul>
