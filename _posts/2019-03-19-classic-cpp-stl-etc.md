---
layout: single
title: "#19. [고전 C++ STL] 기타(작성중)"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> **모던 C++**
> * `static_assert()`가 추가되어 컴파일 타임 진단이 가능해 졌습니다.([static_assert](https://tango1202.github.io/mordern-cpp/mordern-cpp-static_assert/) 참고)
> * `tuple`은 다수의 요소를 관리할 수 있는 데이터 전달용 개체를 손쉽게 만듭니다.([tuple](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-tuple/) 참고)
> * [system_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-chrono/#clock), [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-chrono/#time-point), [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-chrono/#duration) 개체를 이용하여 좀더 다양한 정확도로 시간을 추적할 수 있습니다.([chrono](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-std-chrono/) 참고)

# pair

`pair`는 두개의 데이터를 저장합니다. 함수의 리턴값등에서 두개의 값을 리턴해야 할 때 일일이 클래스를 만들어 이용하기 보다는 `pair`를 이용하여 가볍게 전달할 수 있습니다.

```cpp
std::pair<int, char> f() {
    return std::pair<int, char>(10, 'a');
}
std::pair<int, char> result = f();
EXPECT_TRUE(result.first == 10 && result.second == 'a');
```

`make_pair()`를 이용하면 좀더 단순하게 만들 수 있습니다.

```cpp
std::pair<int, char> g() {
    return std::make_pair(10, 'a');
}
std::pair<int, char> result = g();
EXPECT_TRUE(result.first == 10 && result.second == 'a');        
```

# 프로그램 유틸리티
https://en.cppreference.com/w/cpp/utility/program


https://en.cppreference.com/w/cpp/types/numeric_limits

https://en.cppreference.com/w/cpp/types/type_info

https://en.cppreference.com/w/cpp/types

# 예외

예외 발생시 예외 정보를 담는 `std::exception`, `std::bad_alloc`등의 표준 예외 개체를 제공합니다.(
[표준 예외 개체](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-object/) 참고)

예외가 `catch()`되지 않았을때 사용할 수 있는 `std::terminate()` 핸들러를 제공합니다.([std::terminate](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/#terminate) 참고)

# 자가 진단

assert 
https://en.cppreference.com/w/cpp/error#System_error



# 날짜 / 시간 

https://en.cppreference.com/w/cpp/chrono/c


# 수학
https://en.cppreference.com/w/cpp/numeric/math

rand
srand
RAND_MAX

**complex**

https://en.cppreference.com/w/cpp/numeric/complex

**valarray**

https://en.cppreference.com/w/cpp/numeric/valarray

# stream
https://en.cppreference.com/w/cpp/io#Stream-based_I.2FO

std::basic_istream
std::basic_ostream



