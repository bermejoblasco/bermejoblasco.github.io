---
layout: page
title: CompartiMOSS
---

<p class="message">
Suelo escribir en la revista CompartiMOSS, aquí tenéis todos los articulos que he escrito en esta revista.
</p>
<div id="archive">
{% for post in site.categories.compartimoss %}
  {% assign currentdate = post.date | date: "%Y" %}
  {% if currentdate != date %}
    {% unless forloop.first %}</ul>{% endunless %}
    <h1 id="y{{post.date | date: "%Y"}}">{{ currentdate }}</h1>
    <ul>
    {% assign date = currentdate %}
  {% endif %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% if forloop.last %}</ul>{% endif %}
{% endfor %}
</div>