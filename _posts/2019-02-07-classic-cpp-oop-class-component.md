---
layout: single
title: "#7. [고전 C++ 개체 지향] 클래스 구성 요소"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

허브 셔터가 알려준 궁극의 모듈화.(pimpl 이디엄이라 한다.)
완벽하게 데이터를 은닉하기 위해 다음처럼 한다. h 에서 struct Impl; // 선언
cpp에서 struct Impl {...}; // 정의

class Date { public: typedef int Day; enum month {jan, feb, mar, }; private: struct Impl {
int d, m, y; }; private: Impl* impl; public: Date() : impl(new Impl) {} ~Date() (delete impl;} private: Date(const Date&) {} const Date& operator =(const Date&) {return this;} public: Day day() const {return impl->d;} void day(Day dd) {impl->d = dd;} public: string toString() const; public: static Date today(); };