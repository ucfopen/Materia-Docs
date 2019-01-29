---
layout: default
title: How to get Materia&trade;
tagline: Get your own Materia&trade;
class: admin
highlighter: no
---
# Materia on Github

Materia is open source, so the server and many of the widgets can be found on [Github](https://github.com/ucfopen/Materia).

## Server Setup Guides

{% assign groups = site.administrators | group_by: 'category' | sort: 'name' %}
{% for g in groups %}
	{% assign pages = g.items | sort: 'title' %}
	{% for p in pages %}
		{% if p.categories contains 'server_setup' %}
[{% if p.menu_title %}{{ p.menu_title}}{% else %}{{ p.title }}{% endif %}]({{ site.baseurl }}{{ p.url }})
		{% endif %}
	{% endfor %}
{% endfor %}
