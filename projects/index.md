---
layout: default
title: Projects
permalink: /projects/
---

# Projects & Case Studies (sanitized)

Below are short summaries and links to detailed lab writeups and PDFs. All public items are sanitized and intended for knowledge-sharing.

{% for file in site.static_files %}
  {% if file.path contains 'projects' and file.extname == '.md' %}
  - [{{ file.basename }}](/{{ file.path }})
  {% endif %}
{% endfor %}

