---
layout: archive
title: "Posts on Emacs"
permalink: /emacs/
author_profile: true
---


{% for page in site.emacs %}
  <h2>
    <a href="{{ site.url }}{{ site.baseurl }}{{ page.url }}">
      {{ page.title }}
    </a>
  </h2>
  <p>{{ page.excerpt | markdownify }}</p>
{% endfor %}


