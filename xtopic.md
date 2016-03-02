---
layout: category
permalink: /xtopic/
---

{% for post in site.categories.xtopic %}
  {% include list-item.html post=post %}
{% endfor %}
