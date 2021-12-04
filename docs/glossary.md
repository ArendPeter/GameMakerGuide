---
layout: default
title: Review
nav_order: 18
---

<table>
<thead><tr><th>Term</th><th>Description</th></tr></thead>
<tbody>
{% assign gs = site.data.glossary | sort:[0] %}
{% for kv in gs %}
<tr> <td>{{ kv[0] }} </td><td> {{ kv[1] }} </td></tr>
{% endfor %}
</tbody>
