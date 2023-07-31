---
layout: single
title: "#5. [고전 C++ STL] 템플릿 인수 추론"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---


# 함수 template 오버로딩

```cpp
template <typename T>
T sqrt(T);
tempate <tylename T>
complex<T> sqrt(complex<T>); double sqrt(double); 
void g(complex<double> z) { 
    sqrt(2); // sqrt<int>(2); 
    sqrt(2.0); // sqrt(double); 
    sqrt(z); // sqrt<double>(complex<T>); 
}
```

**오버로딩 스탭**
1. 먼저 다음의 2개의 특수화된 템플릿 함수 후
보 생성
sqrt<complex<double>>(complex<double>) sqrt<double>(complex<double>) 2. 더 특수화된 버전 선택
T sqrt(T) 보다 complex<T> >
sqrt(complex<T>) 가 더 특수화 되었다. 3. 보통 함수 sqrt와 함수 오버로딩 규칙 적용. 단, 특수화된 템플릿 함수는 승격/표준변환/사용
자정의 변환을 적용하지 않음
4. 보통함수와 템플릿 특수화 버전만 남으면 보
통함수 선택

```cpp
template <typename T> 
T max(T, T);
 max(1, 2); // max<int>(1, 2) 
 max('a', 'b'); // max<char>('a', 'b')
  max(2.7, 4.9); // max<double>(2.7, 4.9) 
  
  const int s = 0;
   max(s, 10); // (X) max<int>((int)s, 10) 
   max('a', 10); // (X) 모호성 에러
```

**해법**

명시적으로 쓰거나
max<int>('a', 10); 오버로딩
inline int max(int i, int j) { return max<int>(i, j); }

