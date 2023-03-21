---
title: "Computer Science Posts"
permalink: /categories/cs/
layout: archive
author_profile: true
---

{% assign posts = site.categories.ComputerScience | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}