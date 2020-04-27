---
layout: page
title: Videos
---

<p class="message">
He tenido la suerte de poder grabar y ser grabado en varias sesiones, aquí las podéis encontar:
</p>
<div id="archive">
{% for post in site.categories.video %}
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