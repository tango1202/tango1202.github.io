---
title: "개발 방법론"
layout: archive
permalink: categories/methodology/
author_profile: true
sidebar: 
    nav: "categories"
---

{% assign posts = site.categories.methodology %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}