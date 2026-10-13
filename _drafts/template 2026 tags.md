---
layout: archive
title: ""
permalink: 
author_profile: true
---

{% include base_path %}
<ul>
{% for pub in site.publications %}
  {% if pub.tags contains '' %}
    <li><a href="{{ pub.url }}">{{ pub.title }}</a></li>
  {% endif %}
{% endfor %}
{% for talk in site.talks %}
  {% if talk.tags contains '' %}
    <li><a href="{{ talk.url }}">{{ talk.title }}</a></li>
  {% endif %}
{% endfor %}
</ul>
```That was the complete template — nothing was cut off. It ends with the closing `</ul>`.

Quick note on where the blanks go: `title: ""` (inside the quotes, add your `#tagname`), `permalink:` (after the colon, add `/tags/tagname/`), and both `contains ''` lines (between the quotes, add your tag name).