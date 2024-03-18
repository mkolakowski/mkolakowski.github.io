---
layout: archive
title: "Reposts"
permalink: /reposts/
author_profile: true
---

Here is a collection of articles I have found useful or helpful, full credit to their writers.

{% if site.author.googlescholar %}
  <div class="wordwrap"></a>.</div>
{% endif %}

{% include base_path %}

{% for post in site.reposts reversed %}
  {% include archive-single.html %}
{% endfor %}
