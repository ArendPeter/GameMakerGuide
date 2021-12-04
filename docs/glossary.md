---
layout: default
title: Glossary
nav_order: 18
---

{% assign gs = site.data.glossary | sort:[0] %}
{% tablerow kv in gs cols: 2 %}
	<a href={{ kv[1] }}>{{ kv[0] }}</a>
{% endfor %}
