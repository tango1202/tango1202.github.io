---
layout: single
title: "#28. [모던 C++] (C++11~) 기타"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) 가변 인자를 활용한 가변 매크로가 추가되어 C언어와의 호환성이 높아졌습니다.
> * (C++11~) [sizeof() 연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/#sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)가 개체를 인스턴스화 하지 않더라도 개체 멤버의 크기를 구할 수 있도록 개선되었습니다.
> * (C++17~) `__has_include` 가 추가되어 `include` 하기 전에 파일이 존재하는지 확인할 수 있습니다.

# (C++11~) 가변 매크로

가변 매크로는 C99 에 도입되었으며, C와의 호환성을 위해 C++11에 추가되었습니다.

[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 사용하는 함수를 매크로 함수([#define() 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#define-%ED%95%A8%EC%88%98) 참고)로 호출할 때 사용합니다.

다음 코드에서처럼 매크로 함수 정의시에 `...`을 사용하고, `__VA_ARGS__`를 이용하여 전달할 수 있습니다.

```cpp

#define MY_SUM(count, ...) T::Sum(count, __VA_ARGS__)

class T {
public:
    static int Sum(int count, ...) {
        int result{0};
        std::va_list paramList; // 가변 인자
        va_start(paramList, count); // 가변 인자 처리 시작
        for (int i{0}; i < count; ++i) {
            result += va_arg(paramList, int); // 가변 인자 추출
        }
        va_end(paramList); // 가변인자 처리 끝
        return result;       
    }
};
// 가변 인자에 인수가 있는 경우
EXPECT_TRUE(T::Sum(3, 1, 2, 3) == 1 + 2 + 3);
EXPECT_TRUE(MY_SUM(3, 1, 2, 3) == 1 + 2 + 3);
```

하지만, 인자가 없는 경우에는 `T::Sum(count, )`로 치환되어 컴파일 오류가 납니다.

```cpp
// 가변 인자에 인수가 없는 경우
EXPECT_TRUE(T::Sum(0) == 0);
EXPECT_TRUE(MY_SUM(0) == 0); // (X) 컴파일 오류. T::Sum(count, ) 로 치환됩니다.
```

이러한 문제 때문에 `##__VA_ARGS__` 가 제공되며, 앞의 `,`를 없애줍니다.

```cpp
#define MY_SUM(count, ...) T::Sum(count, __VA_ARGS__)
#define MY_SUM2(count, ...) T::Sum(count, ##__VA_ARGS__)

// 가변 인자에 인수가 없는 경우
EXPECT_TRUE(T::Sum(0) == 0);
EXPECT_TRUE(MY_SUM(0) == 0); // (X) 컴파일 오류. T::Sum(count, ) 로 치환됩니다.
EXPECT_TRUE(MY_SUM2(0) == 0); // (O) 
```

# (C++11~) 멤버 sizeof() 연산자

```cpp
class T {
public:
    int m_X;
};

// C++03 에서는 컴파일 오류
// C++11 부터 허용
EXPECT_TRUE(sizeof(T::m_X) == sizeof(int));
```

# (C++17~) __has_include

파일이 존재하는지 확인하는 전처리기 입니다.

```cpp
#if __has_include(<optional>)
#  include <optional>
#  endif
```