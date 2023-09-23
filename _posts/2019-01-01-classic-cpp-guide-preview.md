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
    std::cout<<"Hello world"<<std::endl;
    std::cin>>val;
    ```

3. [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)

    이름 충돌을 피하기 위한 유효 범위를 제공합니다.

    ```cpp
    using namespace std;
    ```

4. [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)
   
   포인터 외에 참조자를 제공합니다. 포인터는 널(`NULL`)이 될 수 있지만, 참조자는 널이 될 수 없습니다.

   ```cpp
   int i;
   int& j = i;
   ```

5. [형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/)
   
   용도에 따라 구분될 수 있게 `static_cast`, `const_cast`, `dynamic_cast`, `reinterpret_cast` 의 형변환 방법을 제공합니다.

6. 예외처리
   
   예외를 발생시키거나 처리할 수 있습니다.

   ```cpp
   try {}
   catch (...) {}
   ```

7. STL : 문자열

    표준 템플릿 라이브러리(Standard Template Library, STL)를 통해 문자열 타입을 제공합니다.

    ```cpp
    #include <string>
    std::string str = "Hello world";
    ```

8.  STL : 컨테이너

    데이터 자료구조를 관리할 수 있는 컨테이너를 제공합니다.(`vector`, `list`, `queue`, `stack`, `deque`, `priority_queue`, `set`, `multiset`, `map`, `multimap`)

    ```cpp
    std::vector<int> data(100); // 기본값으로 초기화된 100개의 요소
    data[0] = 10;
    ```

9. STL : 이터레이터(반복자)

    컨테이너의 원소 탐색을 위해 이터레이터(반복자)를 제공합니다.

    ```cpp
    std::vector<int> datas;

    for(std::vector<int>::iterator itr = datas.begin(); itr != datas.end(); ++itr) {
        std::cout<<(*itr)<<std::endl;
    }
    ```

10. STL : 알고리즘
    
    일련의 동작에 대한 표준 알고리즘을 제공합니다.(`for_each`, `find`, `count`, `replace`, `copy`, `sort` 등)

11. STL : 스마트 포인터
   
    `auto_ptr`을 제공합니다.(모던 C++에선 사라졌습니다.)

