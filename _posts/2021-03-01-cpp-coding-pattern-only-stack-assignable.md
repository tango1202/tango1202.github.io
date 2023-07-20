---
layout: single
title: "#12. [C++ 코딩 패턴] OnlyStackAssignable - 스택에만 생성할 수 있는 개체"
categories: "cpp-coding-pattern"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

```cpp
// 스택만 할당할 수 있는 개체
class OnlyStackAssignable {
protected: 
    OnlyStackAssignable() {} // 상속해서만 사용 가능
    ~OnlyStackAssignable() {}
private:
    static void* operator new(std::size_t sz) {return NULL;} // 누군가가 접근하면 private여서 컴파일 오류
};
```