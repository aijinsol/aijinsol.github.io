---
title: "Python Posts"
permalink: /categories/python/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Python | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}