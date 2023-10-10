---
title: "24개의 개발 원칙"
layout: archive
permalink: categories/principle/
author_profile: true
sibling-post: false
sidebar: 
    nav: "docs"
---

{% assign posts = site.categories.principle %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}