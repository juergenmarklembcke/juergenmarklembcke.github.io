---
layout: archive
title: "#local-economics"
permalink: /tags/local-economics/
author_profile: true
---

{% include base_path %}
<ul>
{% for pub in site.publications %}
  {% if pub.tags contains 'local-economics' %}
    <li><a href="{{ pub.url }}">{{ pub.title }}</a></li>
  {% endif %}
{% endfor %}
{% for talk in site.talks %}
  {% if talk.tags contains 'local-economics' %}
    <li><a href="{{ talk.url }}">{{ talk.title }}</a></li>
  {% endif %}
{% endfor %}
</ul>
