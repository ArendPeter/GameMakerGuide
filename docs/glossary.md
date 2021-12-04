---
layout: default
title: Glossary
nav_order: 18
---

<table>
{% assign gs = site.data.glossary | sort:[0] %}
{% tablerow kv in gs cols: 3 %}
	<a href="{{ kv[1] }}">{{ kv[0] }}</a>
{% endtablerow %}
</table>
