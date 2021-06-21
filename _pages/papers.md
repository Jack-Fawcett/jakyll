---
title: Papers of Interest
permalink: /papers/
classes: wide
---

The papers I have read along the way
<table>
  {% for row in site.data.paper %}
    {% if forloop.first %}
    <tr>
      {% for pair in row %}
        <th>{{ pair[0] }}</th>
      {% endfor %}
    </tr>
    {% endif %}

    {% tablerow pair in row %}
      {{ pair[1] }}
    {% endtablerow %}
  {% endfor %}
</table>