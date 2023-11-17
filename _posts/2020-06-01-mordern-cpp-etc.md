---
layout: single
title: "#32. [모던 C++] (C++11~) 기타"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [alignas() 와 alignof()](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-alignas-alignof)를 이용하여 [메모리 정렬 방식](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)을 표준화 했습니다.
> * (C++11~) 파라메터 팩을 이용한 [가변 매크로](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EA%B0%80%EB%B3%80-%EB%A7%A4%ED%81%AC%EB%A1%9C)가 추가되어 C언어와의 호환성이 높아졌습니다.
> * (C++11~) [멤버의 `sizeof()`](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EB%A9%A4%EB%B2%84-sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)시 동작이 개선되어 개체를 인스턴스화 하지 않더라도 개체 멤버의 크기를 구할 수 있습니다.
> * (C++17~) [__has_include](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c17-__has_include)가 추가되어 [#include](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#include) 하기 전에 파일이 존재하는지 확인할 수 있습니다.
> * (C++20~) [volatile의 일부가 deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c20-volatile-%EC%9D%BC%EB%B6%80-deprecate)되었습니다.
> * (C++20~) [__VA_OPT__](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c20-va_opt)가 추가되어 [가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)가 있을 경우에는 괄호 안의 값으로 치환하고, 없을 경우에는 그냥 비워둡니다.  
> * (C++20~) [__has_cpp_attribute() 매크로 함수](??)가 추가되어 C++11부터 추가된 [attirbute](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/)가 지원되는지 확인 할 수 있습니다.
> * (C++20~) [언어 기능 지원](??) 매크로를 이용하여 컴파일러가 C++11부터 추가된 언어 기능을 지원하는지 테스트 할 수 있습니다.

# (C++11~) alignas(), alignof()

기존에는 `#pragma pack`을 이용하여 비표준 방식으로[메모리 정렬](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)을 했는데요(*[개체 크기와 메모리 정렬](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC) 참고*),

C++11 부터는 [alignas() 와 alignof()](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-alignas-alignof) 로 이를 표준화하였습니다.

|항목|내용|
|--|--|
|`alignof()` (C++11~)|주어진 타입의 [메모리 정렬](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC) 크기를 구합니다.|
|`alignas()` (C++11~)|주어진 값으로 [메모리 정렬](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EA%B0%9C%EC%B2%B4-%ED%81%AC%EA%B8%B0%EC%99%80-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%A0%95%EB%A0%AC)을 합니다. 단, 2, 4, 8, 16... 단위로 정렬하며, 내부 멤버중 제일 큰 값보다 작으면 무시됩니다.|

```cpp
// 4byte 단위로 정렬합니다.
class alignas(alignof(int)) A_11 {
    char m_A;
    int m_B;
};

// 2, 4, 8, 16... 단위로 정렬하며, 내부 멤버중 제일 큰값보다 커야 합니다.
class alignas(8) B_11 {
    char m_A[13];
    int m_B; 
};

// int 보다 적은값이므로 무시되고 alignof(int) 크기로 정렬됩니다.
class alignas(2) C_11 {
    char m_A[13];
    int m_B; 
};

// 4byte 단위로 멤버 변수가 할당 되므로 char(1) + int(4) = 5 이므로 4 * 2 개 영역에 할당됨
EXPECT_TRUE(alignof(A_11) == 4 && sizeof(A_11) == 4 * 2);

// 8byte 단위로 멤버 변수가 할당 되므로 13 + int(4) = 17 이므로 8 * 3 개 영역에 할당됨
EXPECT_TRUE(alignof(B_11) == 8 && sizeof(B_11) == 8 * 3); 

// 4byte 단위로 멤버 변수가 할당 되므로 13 + int(4) = 17 이므로 4 * 5 개 영역에 할당됨
EXPECT_TRUE(alignof(C_11) == 4 && sizeof(C_11) == 4 * 5); 
```

# (C++11~) 가변 매크로

[가변 매크로](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EA%B0%80%EB%B3%80-%EB%A7%A4%ED%81%AC%EB%A1%9C)는 C99 에 도입되었으며, C와의 호환성을 위해 C++11에 추가되었습니다.

[가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)를 사용하는 함수를 [매크로 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98)로 호출할 때 사용합니다.

다음 코드에서처럼 [매크로 함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%ED%95%A8%EC%88%98) 정의시에 `...`을 사용하고, `__VA_ARGS__`를 이용하여 전달할 수 있습니다.

```cpp

#define MY_SUM_11(count, ...) T::Sum(count, __VA_ARGS__)

class T {
public:
    static int Sum(int count, ...) {
        int result{0};
        std::va_list paramList; // 가변 인자
        va_start(paramList, count); // 가변 인자 처리 시작
        for (int i{0}; i < count; ++i) {
            result += va_arg(paramList, int); // 가변 인자 추출
        }
        va_end(paramList); // 가변 인자 처리 끝
        return result;       
    }
};
// 가변 인자에 인수가 있는 경우
EXPECT_TRUE(T::Sum(3, 1, 2, 3) == 1 + 2 + 3);
EXPECT_TRUE(MY_SUM_11(3, 1, 2, 3) == 1 + 2 + 3);
```

하지만, 인수가 없는 경우에는 `T::Sum(count, )`로 치환되어 컴파일 오류가 납니다.

```cpp
// 가변 인자에 인수가 없는 경우
EXPECT_TRUE(T::Sum(0) == 0);
EXPECT_TRUE(MY_SUM_11(0) == 0); // (X) 컴파일 오류. T::Sum(count, ) 로 치환됩니다.
```

이러한 문제 때문에 `##__VA_ARGS__` 가 제공되며, 앞의 `,`를 없애줍니다.

```cpp
#define MY_SUM(count, ...) T::Sum(count, __VA_ARGS__)
#define MY_SUM2_11(count, ...) T::Sum(count, ##__VA_ARGS__)

// 가변 인자에 인수가 없는 경우
EXPECT_TRUE(T::Sum(0) == 0);
EXPECT_TRUE(MY_SUM(0) == 0); // (X) 컴파일 오류. T::Sum(count, ) 로 치환됩니다.
EXPECT_TRUE(MY_SUM2(0) == 0); // (O) 
```

# (C++11~) 멤버 sizeof() 연산자

[멤버의 `sizeof()`](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c11-%EB%A9%A4%EB%B2%84-sizeof-%EC%97%B0%EC%82%B0%EC%9E%90)시 동작이 개선되어 개체를 인스턴스화 하지 않더라도 개체 멤버의 크기를 구할 수 있습니다.

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
#endif
```

# (C++20~) volatile 일부 deprecate

다음 4가지 경우에 대해 `volatile` 사용이  [deprecate](https://tango1202.github.io/mordern-cpp/mordern-cpp-preview/#deprecateremove)되었습니다.

* 복합 대입

    ```cpp
    int a, b;
    volatile int volatile_val;

    // 복합 대입 deprecate
    volatile_val = a; // (O)
    b = volatile_val; // (O)

    b = volatile_val = a; // (X) volatile_val에 한번 접근하는지 두번 접근하는지 모호
    ```

* 산술형 대입 연산, 증감 연산

    ```cpp
    volatile int volatile_val;

    // 산술형 대입 연산자, 증감 연산자 deprecate
    volatile_val = volatile_val + 1; // (O)
    volatile_val += 1; // (X) volatile_val에 한번 접근하는지 두번 접근하는지 모호
    ```

* 함수 인자와 리턴값

    ```cpp
    // 함수 인자와 리턴값 deprecate
    volatile int f(); // (X)
    void g(volatile int); // (X)
    ```

* 구조적 바인딩

    ```cpp
    // 구조적 바인딩 deprecate
    sturct A {
        volatile int x;
        volatile int y;
    };
    A a;
    auto [x_17, y_17]{a}; // (X)    
    ```

# (C++20~) __VA_OPT__

 [__VA_OPT__](https://tango1202.github.io/mordern-cpp/mordern-cpp-etc/#c20-va_opt)가 추가되어 [가변 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EA%B0%80%EB%B3%80-%EC%9D%B8%EC%9E%90)가 있을 경우에는 괄호 안의 값으로 치환하고, 없을 경우에는 그냥 비워둡니다.  

 ```cpp
int Sum(int a, int b, int c, int d) {return a + b + c + d;}
int Sum(int a) {return a;}
#define MY_FUNC_20(...) Sum(10 __VA_OPT__(,) __VA_ARGS__) // 가변 인수가 있다면 ,를 넣습니다.
EXPECT_TRUE(MY_FUNC_20(1, 2, 3) == 10 + 1 + 2 + 3); // f(10, 1, 2, 3) 가변 인수가 있다면 , 를 넣습니다.
EXPECT_TRUE(MY_FUNC_20() == 10); // f(10) 가변 인수가 없다면 ,를 넣지 않습니다.
```

# __has_cpp_attribute() 매크로 함수

C++20 부터는 [__has_cpp_attribute() 매크로 함수](??)가 추가되어 C++11부터 추가된 [attirbute](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/)가 지원되는지 확인 할 수 있습니다.

테스트할 수 있는 [attirbute](https://tango1202.github.io/mordern-cpp/mordern-cpp-attribute/)는 [https://en.cppreference.com/w/cpp/feature_test](https://en.cppreference.com/w/cpp/feature_test)를 참고하시기 바랍니다.

```cpp
#if __has_cpp_attribute(deprecated)
    std::cout << "Support deprecated" << std::endl; // deprecated를 지원합니다.
#else 
    std::cout << "No Support deprecated" << std::endl;
#endif
```

# 언어 지원 테스트

C++20 부터는 [언어 기능 지원](??) 매크로를 이용하여 컴파일러가 C++11부터 추가된 언어 기능을 지원하는지 테스트 할 수 있습니다.

테스트할 수 있는 항목은 [https://en.cppreference.com/w/cpp/feature_test](https://en.cppreference.com/w/cpp/feature_test)를 참고하시기 바랍니다.

```cpp
#if __cpp_char8_t
    std::cout << "Support char8_t" << std::endl; // char8_t를 지원합니다.
#else 
    std::cout << "No Support char8_t" << std::endl;
#endif  
```
