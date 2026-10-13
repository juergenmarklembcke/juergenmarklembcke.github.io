---
layout: archive
title: "#street-design"
permalink: /tags/#street-design/
author_profile: true
---

{% include base_path %}
<ul>
{% for pub in site.publications %}
  {% if pub.tags contains 'street-design' %}
    <li><a href="{{ pub.url }}">{{ pub.title }}</a></li>
  {% endif %}
{% endfor %}
{% for talk in site.talks %}
  {% if talk.tags contains 'street-design' %}
    <li><a href="{{ talk.url }}">{{ talk.title }}</a></li>
  {% endif %}
{% endfor %}
</ul>
