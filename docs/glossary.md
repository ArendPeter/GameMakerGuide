---
layout: default
title: Glossary
nav_order: 18
---

<table style="border: 0;">
{% assign gs = site.data.glossary | sort:[0] %}
{% for kv in gs %}
<tr> <td><a href={{ kv[1] }}>kv[0]</a> </td></tr>
{% endfor %}
</table>
