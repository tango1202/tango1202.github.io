---
layout: single
title: "#3. [고전 C++ 가이드] 표준 예외 개체"
categories: "classic-cpp-exception"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **모던 C++**
> * (C++11~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)은 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 예외를 나열하는 것보다 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept)로 예외를 방출하느냐 안하느냐만 관심을 둡니다.
> * (C++17~) [동적 예외 사양](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91) 관련해서 [throw()](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EB%8F%99%EC%A0%81-%EC%98%88%EC%99%B8-%EC%82%AC%EC%96%91)가 [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove) 되었습니다. 이제 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)만 사용해야 합니다. 

# 개요

[throw](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#%EC%98%88%EC%99%B8-%EB%B0%9C%EC%83%9D%EA%B3%BC-%ED%83%90%EC%A7%80try-catch-throw) 에서 예외를 발생시킬 때 예외 개체를 만들어 예외에 대한 정보를 담아 전달합니다.(`catch()`에서 예외 개체를 전달받을때, 예외 개체가 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)되지 않도록 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 받는게 좋습니다.)

표준에서 예외 개체는 다음과 같은 상속 구조를 가집니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e180dc05-a0f6-4879-abc1-376061be3162)


표준 예외 종류에 대해서는 모던 C++ STL의 [표준 예외](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-diagnostics/#%ED%91%9C%EC%A4%80-%EC%98%88%EC%99%B8) 를 참고하기 바랍니다.
