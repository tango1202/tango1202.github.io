---
title: "주석론"
layout: archive
permalink: categories/comment/
author_profile: true
sidebar: 
    nav: "categories"
---

{% assign posts = site.categories.comment %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}