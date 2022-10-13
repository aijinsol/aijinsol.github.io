---
title: "Project Posts (PRJ)"
permalink: /categories/project/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Project | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}