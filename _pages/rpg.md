layout: archive
title: "RPG stuff"
permalink: /rpg/
author_profile: true
---

Here is a collection of articles I have found useful or helpful, full credit to their writers.

{% if site.author.googlescholar %}
  <div class="wordwrap"></a>.</div>
{% endif %}

{% include base_path %}

{% for post in site.rpg reversed %}
  {% include archive-single.html %}
{% endfor %}
