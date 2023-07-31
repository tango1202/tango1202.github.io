---
layout: single
title: "#3. [고전 C++ STL] traits(취급 특성)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

**사례**

영문에서는 영어가 먼저 정렬되고, 국문에서는 한글이 먼저 정렬되게 하고 싶다. 뭐 if - else 써서 해도 되고 객체 지향적으로 strategy 패턴을 써서 해도 되는데, 이참에 일반화
프로그래밍 방식으로 하고 싶다. 어케 하지?

**해법**

기본 템플릿 인자를 쓸수도 있다.
tempate<typename T, typename Traits = Korea<T>)
int compare(T, T); 와 같이 정의되었다면, compare(s1, s2);

```cpp
template<typename T>
class Korea { 
    static int Compare(T, T); // 한국식으로 비교
};
// Traits 정책에 맞게 비교
template<typename T, typename Traits>
int compare(T s1, T s2) {
     return Traits<T>::Compare(s1, s2); 
}
void f() { 
    compare<string, Korea<string> >(s1, s2); 
    compare<wstring, English<wstring>>(s1, s2); 
}
```
