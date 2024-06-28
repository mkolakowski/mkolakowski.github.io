---
layout: archive
title: "Powershell Things"
permalink: /powershell/
author_profile: true
---

Here is a collection of Powershell stuff I like.

{% if site.author.googlescholar %}
  <div class="wordwrap"></a>.</div>
{% endif %}

{% include base_path %}

{% for post in site.powershell reversed %}
  {% include archive-single.html %}
{% endfor %}
