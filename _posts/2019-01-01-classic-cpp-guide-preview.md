---
layout: single
title: "#1. [고전 C++ 가이드] 주요 구성 요소 미리보기"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

C++ 의 주요 구성 요소는 하기와 같습니다.

1. `main()` 함수
   
   어플리케이션의 시작 함수입니다.

    ```cpp
    void main(void) {}
    ```

2. 입출력

    ```cpp
    std::cout << "Hello world" << std::endl;
    std::cin>>val;
    ```

3. [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)

    이름 충돌을 피하기 위한 [유효 범위](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-scope/)를 제공합니다.

    ```cpp
    using namespace std;
    ```

4. [포인터와 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)
   
   포인터 외에 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 제공합니다. 포인터는 널(`NULL`)이 될 수 있지만, [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)는 널이 될 수 없습니다.

   ```cpp
   int i;
   int& j = i;
   ```
5. [기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/), [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/), [구조체, 클래스, 공용체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)

    데이터를 저장하고 처리합니다.

    특히 [구조체와 클래스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-struct-class-union/)는 데이터와 이를 처리하는 함수를 응집하여 개체 지향 프로그래밍을 가능하게 합니다. 

6. [리터럴](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/)

    `10`, `20`등 값을 나타내는 상수입니다.

7. [연산자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-operators/)

    `+`, `-`등 데이터들을 연산합니다.

8. [제어문](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-statements/)

    `if()`, `for()`등 구문을 분기하거나 반복합니다.

9. [함수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/)

    코드를 재활용하여 구조적 프로그래밍을 가능하게 합니다.
  
10. [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/)
   
    용도에 따라 구분하여 형변환 할 수 있도록 [static_cast, const_cast, dynamic_cast, reinterpret_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)를 제공합니다.

1. [템플릿](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template/)

    타입에 따라 클래스나 함수를 생성하는 틀입니다.

2. [예외처리](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-mechanism/)
   
    예외를 방출하거나 처리할 수 있습니다.

    ```cpp
    try {}
    catch (...) {}
    ```

3. [STL : 문자열](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-string/)

    표준 템플릿 라이브러리(*Standard Template Library, STL*)를 통해 문자열 타입을 제공합니다.

    ```cpp
    #include <string>
    std::string str = "Hello world";
    ```

4.  [STL : 컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/)

    데이터 자료구조를 관리할 수 있는 [컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/)를 제공합니다.(*[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/), `list`, `queue`, `stack`, `deque`, `priority_queue`, `set`, `multiset`, `map`, `multimap`*)

    ```cpp
    std::vector<int> data(100); // 기본값으로 초기화된 100개의 요소
    data[0] = 10;
    ```

5. [STL : 이터레이터(반복자)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/)

    [컨테이너](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/)의 요소 탐색을 위해 [이터레이터(반복자)](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-iterator/)를 제공합니다.

    ```cpp
    std::vector<int> datas;

    for(std::vector<int>::iterator itr = datas.begin(); itr != datas.end(); ++itr) {
        std::cout << (*itr) << std::endl;
    }
    ```

6. [STL : 알고리즘](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-algorithm/)
    
    일련의 동작에 대한 표준 알고리즘을 제공합니다.(*[for_each()](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-algorithm/#for_each), `remove()` `find()`, `count()`, `replace()`, `copy()`, `sort()` 등*)

7. [STL : 스마트 포인터](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)
   
    [auto_ptr](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-auto_ptr/)을 제공합니다.(*모던 C++에선 사라졌습니다.*)

