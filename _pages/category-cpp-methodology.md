---
title: "개발 방법론"
layout: archive
permalink: categories/methodology/
author_profile: true
sibling-post: false
sidebar: 
    nav: "docs"
---

{% assign posts = site.categories.methodology %}
{% for post in (posts) reversed %} {% include archive-single.html type=page.entries_layout %} {% endfor %}