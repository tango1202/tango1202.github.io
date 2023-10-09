---
title: "개발설정"
layout: archive
permalink: categories/dev-setting
author_profile: true
sidebar: 
    nav: "categories"
---

{% assign posts = site.categories.dev-setting %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}