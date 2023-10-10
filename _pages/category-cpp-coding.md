---
title: "코딩을 위한 좋은 습관"
layout: archive
permalink: categories/coding/
my-category: "coding"
author_profile: true
sidebar: 
    nav: "categories"
---

{% assign posts = site.categories.coding %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}