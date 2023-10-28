---
layout: single
title: "#12. [C++ 코딩 패턴] OnlyStackAssignable - 스택 또는 힙에만 생성할 수 있는 개체"
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

[힙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-memory-segment/#%ED%9E%99)에만 할당 가능한 개체

```cpp
class OnlyHeapAssignable {
protected:
    OnlyHeapAssignable() {} // 상속해서만 사용 가능
    ~OnlyHeapAssignable() {}
public:
    void Destroy() const {delete this;}
};
```