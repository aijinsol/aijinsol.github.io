---
title: "Git Posts"
permalink: /categories/git/
layout: archive
author_profile: true
---

{% assign posts = site.categories.Git | sort:'date' | reverse %}

{% for post in posts %}
    {% include archive-single.html %}
{% endfor %}