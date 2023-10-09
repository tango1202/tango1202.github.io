---
title: "디자인 패턴"
layout: archive
permalink: categories/pattern/
author_profile: true
sidebar: 
    nav: "categories"
---

{% assign posts = site.categories.pattern %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}