---
layout: page
category: eyasoft
title: "Category: EyaSoft"
permalink: /categories/eyasoft
---

{% for p in site.categories[page.category] %}
- [{{ p.title | escape }}]({{ site.baseurl }}{{ p.url }}) ({{ p.date | date_to_string: "ordinal" }})
{% endfor %}