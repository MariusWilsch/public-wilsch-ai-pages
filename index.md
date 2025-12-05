---
layout: default
title: Published Documents
---

# Published Documents

## Global

{% for page in site.pages %}{% if page.path contains 'global/' and page.path != 'global/index.md' %}- [{{ page.path | remove: 'global/' | remove: '.md' | replace: '-', ' ' | capitalize }}]({{ page.url | relative_url }})
{% endif %}{% endfor %}

## Project

{% for page in site.pages %}{% if page.path contains 'project/' and page.path != 'project/index.md' %}- [{{ page.path | remove: 'project/' | remove: '.md' | replace: '-', ' ' | capitalize }}]({{ page.url | relative_url }})
{% endif %}{% endfor %}
