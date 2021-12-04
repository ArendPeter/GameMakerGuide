---
layout: default
title: Glossary
nav_order: 18
---

<table style="border: 0;">
{% assign gs = site.data.glossary | sort:[0] %}
{% for kv in gs %}
<tr> <td>{{ kv[0] }} </td><td> {{ kv[1] }} </td></tr>
{% endfor %}
</table>
