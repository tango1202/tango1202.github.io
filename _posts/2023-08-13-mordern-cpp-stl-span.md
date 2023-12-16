---
layout: single
title: "#43. [모던 C++ STL] (C++20~) span
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

span을 이용하여 연속된 시퀀스의 참조를 전달 받을 수 있음.

View는 원본을 수정하지 않지만, span은 원본을 수정함.

# span 초기화

C스타일 배열, array, vector, 메모리, string 으로 초기화 할 수 있음