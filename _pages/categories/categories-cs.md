---
title: "Computer Science Posts"
permalink: /categories/cs/
layout: archive
author_profile: true
---

{% assign posts = site.categories.CS | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}