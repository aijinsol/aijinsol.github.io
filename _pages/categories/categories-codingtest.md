---
title: "Coding Test Practices"
permalink: /categories/codingtest/
layout: archive
author_profile: true
---

{% assign posts = site.categories.CodingTest | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}