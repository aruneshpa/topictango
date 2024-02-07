---
layout: archive
title: "Notes and Quotes from Blogs, Books and Podcasts!"
permalink: /notes/
author_profile: true
---


{% for page in site.notes %}
  <h2>
    <a href="{{ site.url }}{{ site.baseurl }}{{ page.url }}">
      {{ page.title }}
    </a>
  </h2>
  <p>{{ page.excerpt | markdownify }}</p>
{% endfor %}
