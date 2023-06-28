---
layout: default
title: Subpages
---

<h1>Subpages</h1>

<ul>
{% for page in site.pages %}
{% if page.url contains page.parent.path %}
  <li><a href="{{ page.url }}">{{ page.title }}</a></li>
{% endif %}
{% endfor %}
</ul>