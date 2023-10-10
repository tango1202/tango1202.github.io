---
title: "디자인 패턴"
layout: archive
permalink: categories/pattern/
author_profile: true
sibling-post: false
sidebar: 
    nav: "docs"
---

{% assign posts = site.categories.pattern %}
{% for post in (posts) reversed %} {% include archive-single.html type=page.entries_layout %} {% endfor %}