---
layout: single
title: "#1. [고전 C++ 예외 안정] 예외 안정 생성자"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

생성 후 대입과정에서 예외가 발생하면 난감해지는 상황 예시. 
생성자에서 예외가 발생하더라도 안정적일 수 있도록 멤버를 핸들러, 스마트 포인터로 작성