---
title: "Algorithm"
permalink: /categories/algorithm/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Algorithm | sort:'date' | reverse %}

{% for post in posts %}
{% include archive-single.html %}
{% endfor %}
