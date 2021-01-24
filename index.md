---
layout: page
title: Home
---


<p class="message">This is the personal blog of robmsmt. To contact me please visit <a href="about">about</a></p>
 

--- 
{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}

