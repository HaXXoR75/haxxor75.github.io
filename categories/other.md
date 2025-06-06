---
layout: page
category: update
title: "Category: Other"
permalink: /categories/other
---

{% for p in site.categories[page.category] %}
- [{{ p.title | escape }}]({{ site.baseurl }}{{ p.url }}) ({{ p.date | date_to_string: "ordinal" }})
{% endfor %}