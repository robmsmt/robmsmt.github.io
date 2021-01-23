---
layout: page
title: Home
---

<h2 class="page-heading">About</h2>

<p class="message">This is the personal blog of robmsmt. To reach out please visit [about](about.html)</p>
 

--- 
{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}

