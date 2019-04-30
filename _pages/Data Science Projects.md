---
layout: archive
permalink: /Data SCience Projects/
title: "Data Science Project Portfolio"
author_profile: true
header:
  image: "/image/spec.jpg"
---

Data Science is my passion. Each set of data contains a story behind it. 
My skill in Python, Machine Learning, Tableau, SQL enriched me with successfull project completions.
  

{% include base_path %}
{% include group-by-array collection=site.posts field="tags" %}

{% for tag in group_names %}
  {% assign posts = group_items[forloop.index0] %}
  <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2>
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}