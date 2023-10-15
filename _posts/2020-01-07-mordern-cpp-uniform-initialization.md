---
layout: single
title: "#7. [모던 C++] (C++11~) 중괄호 초기화, (C++17~) 중괄호 초기화에서 auto 추론의 새로운 규칙"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)가 추가되어 클래스, 배열, 구조체를 일관성 있게 초기화 할 수 있습니다.
> * (C++11~) [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)로 함수 인수 전달, 리턴문 작성을 간소화할 수 있습니다.
> * (C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)시 인자의 암시적 형변환을 일부 차단하여, 코딩 계약이 개선되었습니다.
> * (C++11~) [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 가 추가되어 `vector`등 컨테이너 요소 추가가 간편해 졌습니다.
> * (C++17~) [중괄호 초기화에서 auto 추론의 새로운 규칙](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#c17-auto-%EC%B6%94%EB%A1%A0%EC%9D%98-%EC%83%88%EB%A1%9C%EC%9A%B4-%EA%B7%9C%EC%B9%99)이 적용되어, [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 로 추론되는 오류가 개선되었습니다.
> * (C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)들이 표준화 되었습니다.


# 중괄호 초기화

[기본 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B8%B0%EB%B3%B8-%EC%B4%88%EA%B8%B0%ED%99%94) 에서도 언급했듯 `()`을 사용하는 초기화 방법은 컴파일러가 다르게 해석(기본 생성자 호출을 함수 선언으로 오해)할 소지가 있습니다. 그리고, 클래스인지, 배열인지, 구조체 인지에 따라 때로는 괄호를 생략하거나, 괄호를 넣거나, 중괄호를 사용해야 하거나 뒤죽 박죽입니다.

```cpp
class T {
    int m_A;
    char m_B;
public:
    T() {}
    T(int a, char b) : m_A(a), m_B(b) {}    
};
T obj(); //(△) 비권장. T를 리턴하는 obj 함수 선언

T obj1; // 기본 생성자로 T 개체 생성
T obj2(T()); // 기본 생성자인 T()로 생성한 개체를 obj의 복사 생성자로 복사 생성
T obj3 = T(); // T obj(T());와 동일
T obj4(10, 'b'); // m_A == 10, m_B == `b`인 T 개체 생성

T arr[] = {T(), T(10, 'b')}; // T 요소 2개인 배열 생성

struct U {
    int m_A;
    char m_B;
};
U objs = {10, 'b'}; // m_A == 10, m_B == `b`인 U 개체 생성
```

C++11 부터는 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)를 제공하여 클래스인지, 배열인지, 구조체의 구분없이 `{}`을 이용하여 일관성 있게 초기화 할 수 있습니다.

```cpp
class T {
    int m_A;
    char m_B;
public:
    T() {}
    T(int a, char b) : m_A(a), m_B(b) {}    
};

T obj1_11{}; // 기본 생성자로 T 개체 생성
T obj2_11{T{}}; // 기본 생성자인 T()로 생성한 개체를 obj의 복사 생성자로 복사 생성
T obj3_11 = T{}; // T obj = T();와 동일
T obj4_11{10, 'b'}; // T(int a, char b) 생성자 호출. m_A == 10, m_B == `b`인 T 개체 생성

T arr_11[]{T{}, T{10, 'b'}}; // T 요소 2개인 배열 생성

struct U {
    int m_A;
    char m_B;
};
U objs_11{10, 'b'}; // m_A == 10, m_B == `b`인 U 개체 생성    
```

# 중괄호 직접 초기화 T t{};

생성자를 호출하고, 초기값을 전달합니다.

1. 기존에는 생성자 호출시 `T()`와 같이 `()`을 사용했는데, `{}`를 사용합니다. 특히 [기본 생성자 호출이 함수 선언으로 인식되는 오류](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)가 말끔히 해결됐습니다.

    ```cpp
    class T {
    public:
        T() {}
        T(int, char) {}
    };
    T a_11{}; // T a(); 는 T를 리턴하는 함수 a의 정의로 인식되어 컴파일 오류가 났었습니다.
    T b_11{10, 'a'}; // T(int, char)로 생성합니다.
    T{10, 'a'}; // T(int, char) 로 임시 개체를 생성합니다.
    T* c_11 = new T{10, 'a'}; // new 시 T(int, char)로 생성한 포인터를 d에 저장합니다.
    delete c_11;
    ```

2. [클래스 멤버 변수 선언](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/)시 초기화에 사용할 수 있습니다.([멤버 선언부 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-initialization/) 참고)

    ```cpp
    class T {
    public:
        int m_A_11{0}; // 멤버 변수를 초기화합니다.
        char m_B_11{'a'}; 
    };     
    ```

3. [클래스 생성자 초기화 리스트](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/#%EC%B4%88%EA%B8%B0%ED%99%94-%EB%A6%AC%EC%8A%A4%ED%8A%B8)에서 사용할 수 있습니다.

    ```cpp
    class T {
    public:
        int m_A_11; 
        char m_B_11;
    public:
        T(int a, char b) :
            m_A_11{a}, // 생성자 초기화 리스트에서 멤버 변수 초기화에서 사용합니다.
            m_B_11{b} {}
    };
    ```

# 중괄호 복사 초기화 T t = {};, t = {};, f({}), return {}

`T t = {};` 표현은 `T t = T{};`의 축약형입니다. 즉 `T t{T{}};`입니다. [중괄호 직접 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-t-t)인 `T{}`로 개체를 생성하고, 복사 생성자를 이용하여 `t`을 복사 생성하는 표현입니다.

1. 생성자 호출시 사용할 수 있습니다.
   
    이때, `T t{T{}};`은 컴파일러 최적화에 의해 `T t{};`와 동일하게 작동할 수 있습니다.

    다음 코드의 실행 결과를 보면,

    ```cpp
    class T {
    public:
        T() {std::cout << "T : Default Constructor" << std::endl;}
        T(const T&) {std::cout << "T : Copy Constructor" << std::endl;}
        T& operator =(const T&) {std::cout << "T : operator =()" << std::endl;return *this;}
    };

    T a_11{}; // 기본 생성자 호출
    T b_11 = {}; // T b_11 = T{};와 동일
    T c_11 = T{}; // T c_11{T{}} 와 동일. 즉 T 기본 생성자를 호출하고, 복사 생성자를 호출. 컴파일러 최적화에 의해 2개의 생성을 1개의 생성으로 바꿈
    ```

    모두 기본 생성자를 1회만 호출하는 것을 알 수 있습니다.

    ```cpp
    T : Default Constructor // T a_11{};
    T : Default Constructor // T b_11 = {};
    T : Default Constructor // T c_11 = T{};
    ```
    > *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)들이 표준화 되었습니다.*

2. 대입문에 사용할 수 있습니다.
   
    ```cpp
    class T {
    public:
        T() {std::cout << "T : Default Constructor" << std::endl;}
        T(const T&) {std::cout << "T : Copy Constructor" << std::endl;}
        T& operator =(const T&) {std::cout << "T : operator =()" << std::endl;return *this;}
    };
    T t_11;
    t_11 = {}; // t = T{};과 동일
    ```
    
    ```cpp
    T : Default Constructor // T t_11;
    T : Default Constructor // t_11 = {}; 에서 {}는 사실 T{} 이므로 기본 생성자 호출
    T : operator =() // t_11 = {}; 에서 = 호출
    ```

3. 함수 인수 전달에 사용할 수 있습니다.

    ```cpp
    void f(T param) {}

    f({}); // f(T{}); 와 동일
    ```

4. 리턴문에 사용할 수 있습니다.

    ```cpp
    class T {
        int m_A;
        char m_B;
    public:
        T() {}
        T(int a, char b) : m_A(a), m_B(b) {}    
    };

    T Func_11() {
        return {10, 'b'}; // return T{10, 'b'}; 와 동일
    }
    ```

# 중괄호 집합 초기화

[집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)의 경우에는 기존에도 `= {}` 표현을 지원했는데요,([배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 와 [구조체 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B5%AC%EC%A1%B0%EC%B2%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 참고)

C++11 부터는 `{}` 도 지원합니다.

1. 요소 갯수 유추
 
    ```cpp
    int arr[] = {0, 1, 2}; // 초기화 갯수 만큼 배열 할당
    int arr_11[]{0, 1, 2}; // 초기화 갯수 만큼 배열 할당   
    ```

2. [자동 제로 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%9E%90%EB%8F%99-%EC%A0%9C%EB%A1%9C-%EC%B4%88%EA%B8%B0%ED%99%94) 

    ```cpp
    int arr[3] = {0, 1,}; // 초기값이 모자르면 0으로 채움
    int arr_11[3]{0, 1,}; // 초기값이 모자르면 0으로 채움
    EXPECT_TRUE(arr[2] == 0 && arr_11[2] == 0);
    ```       

# 인자의 암시적 형변환 차단

중괄호 초기화는 생성자 인자의 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 기존보다는 좀더 차단해 줍니다. 코딩 계약이 좀더 단단해 졌지만, [사용자 정의 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)은 여전히 막지 못합니다.

1. 실수에서 정수로 변환을 차단합니다.

    `T a(3.14);`은 `double`형이 `int`로 암시적으로 형변환되어 호출됩니다만([오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고), `T b{3.14};` 은 컴파일 오류를 발생시킵니다.

    ```cpp
    class T {
    public:
        explicit T(int) {};
    };

    T a(3.14); // (△) 비권장. double 이 int로 암시적으로 변환되어 초기화 됩니다.
    T b_11{3.14}; // (X) 컴파일 오류. 암시적 변환을 차단합니다.
    ```

2. `double`에서 `float`으로의 변환을 차단합니다. 단, 상수 표현식이고, 해당 값을 `float`으로 저장할 수 있다면 허용합니다.(`int`에서 `char` 변환도 동일합니다.)

    ```cpp
    class T {
    public:
        explicit T(float) {};
    };  

    T a_11{3.14}; // 3.14는 float이 저장할 수 있어서 허용

    double doublVal = 3.14;   
    T b_11{doublVal}; // (X) 컴파일 오류. double을 float으로 변환하는건 차단합니다.
    ```

3. `int`에서 `char` 변환도 `double`에서 `float`의 변환과 동일합니다.

    ```cpp
    class T {
    public:
        explicit T(char) {};
    }; 
    
    T a_11{10}; // 10은 char에서 저장할 수 있어서 허옹
    T b_11{255}; // (X) 컴파일 오류. 255는 char에서 저장할 수 없음. 

    int intVal = 10;  
    T c_11{intVal}; // (X) 컴파일 오류. int를 char로 변환하는건 차단합니다.
    ```

4. 포인터 타입에서 `bool`로의 변환을 차단합니다.
   
5. 사용자가 [형변환 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90) 를 작성하면 암시적 형변환이 허용됩니다.
   
   ```cpp
    class A {};
    class B {
    public:
        B(A) {} // explicit가 없는 형변환 생성자. A로 암시적으로 생성됩니다.
    };  
    class T {
    public:
        explicit T(B) {}
    };     

    A a;
    T t_11{a}; // (△) 비권장. A->B로의 암시적 변환을 허용하면 차단되지 않습니다.   
   ```

# 중괄호 초기화 중첩

배열을 초기화 할때 다음처럼 [중괄호로 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/) 할 수 있는데요,

```cpp
class A {
    int m_X;
    int m_Y;
public:
    A(int x, int y) : m_X(x), m_Y(y) {}
};

A arr_11[]{
    A{1, 2}, // 배열의 첫번째 요소
    A{2, 3} // 배열의 두번째 요소
};
```

[중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)의 축약 표현을 사용하면 다음처럼 표현할 수 있습니다.

```cpp
A arr_11[]{
    {1, 2}, // A{1, 2} 와 동일
    {2, 3} // A{2, 3} 과 동일
};
```

또한 내부 멤버 변수 개체를 초기화 하는데에도 축약형으로 표현할 수 있습니다.

```cpp
class A {
    int m_X;
    int m_Y;
public:
    A(int x, int y) : m_X(x), m_Y(y) {}
};
class B {
    int m_Val;
    A m_A;
public:
    B(int val, A a) : m_Val(val), m_A(a){}
};

B b_11{1, {2, 3}}; // B b_11{1, A{2, 3}};와 동일
```

멤버 변수가 `public`인 [집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)이라면, 내부적으로는 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 이용하며, 동일한 형태로 초기화 할 수 있습니다.

```cpp
class A {
public:
    int m_X;
    int m_Y;
};
class B {
public:
    int m_Val;
    A m_A;
};

B b_11{1, {2, 3}}; // B b_11 = B{1, A{2, 3}};와 동일
```

# initializer_list

[initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 가 추가되어 `vector`등 컨테이너 요소 추가가 간편해 졌습니다. 
 
 C++11의 컨테이너는 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)를 이용하여 쉽게 초기값을 입력할 수 있도록 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 을 사용한 생성자를 제공합니다.

`vector`의 경우 요소를 입력할때 `push_back()`을 사용하여 코드 작성이 번거로웠는데([vector 의 삽입과 삭제](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C) 참고), [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 을 사용한 생성자를 이용하면 다음처럼 좀 더 간편하게 요소 값을 초기화 할 수 있습니다.

```cpp
// 이전 방식
std::vector<int> v;
v.push_back(1);
v.push_back(2);
EXPECT_TRUE(v[0] == 1 && v[1] == 2);

// {} 를 이용하여 initializer_list로 초기화
std::vector<int> v1_11{1, 2};
EXPECT_TRUE(v1_11[0] == 1 && v1_11[1] == 2);       

// = {} 형태도 지원
std::vector<int> v2_11 = {1, 2};
EXPECT_TRUE(v2_11[0] == 1 && v2_11[1] == 2); 
```
# initializer_list 멤버 함수 

|항목|내용|
|--|--|
|`size()` (C++11~)|요소 갯수를 리턴합니다.|
|`begin()` (C++11~)|첫번째 요소의 이터레이터를 리턴합니다.|
|`end()` (C++11~)|마지막 요소의 다음 위치의 이터레이터를 리턴합니다.|

# initializer_list의 암시적 생성

다음의 경우 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/) 표현식은 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)를 암시적으로 자동 생성합니다.

1. 생성자 인자와 함수의 인자가 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 인 경우

    ```cpp
    class T_11 {
    public:
        explicit T_11(std::initializer_list<int>) {}
    };
    T_11 t{{1, 2, 3}}; // {1, 2, 3} 은 initializer_list를 생성해서 전달합니다.
    ```

2. 복사 대입 연산의 대상이 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 인 경우

    ```cpp
    std::initializer_list<int> data_11;
    data_11 = {1, 2, 3}; // {1, 2, 3} 은 initializer_list를 생성해서 전달합니다.         
    
    std::vector<int> v;
    auto itr = data_11.begin();
    auto endItr = data_11.end();
    for (;itr != endItr; ++itr) {
        v.push_back(*itr);
    }
    ```

3. [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)에서 범위 표현식으로 사용하는 경우
   
   ```cpp
    std::vector<int> v;
    for (auto a_11 : {1, 2, 3}) { // {1, 2, 3} 은 initializer_list를 생성하고 범위 기반 for문에서 사용됩니다.
        v.push_back(a_11);
    }  
   ```

# 중괄호 초기화 우선 순위

[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)는 기존 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)와의 호환성과 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 의 편의성을 위해 다음의 우선 순위에 맞춰 동작합니다.

1. 문자 배열에서 단일 문자열 상수로 초기화 하면 문자열 상수로 초기화 합니다.

    ```cpp
    char arr_11[] = {"abc"}; // char arr_11[] = "abc"; 와 동일
    ```
2. [집합 타입](https://tango1202.github.io/mordern-cpp/mordern--category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)인 경우 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 합니다.

3. 중괄호가 비어 있고 기본 생성자가 있으면, 기본 생성자를 호출합니다.

4. [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)를 사용한 생성자가 있으면 해당 생성자를 호출합니다. 

    ```cpp
    class T_11 {
    public:
        T_11(int, int, int, int, int) {}
        T_11(std::initializer_list<int>) {}
        T_11(std::initializer_list<int>, int, int) {}
    };
    T_11 a{1, 2, 3, 4, 5}; // T_11(std::initializer_list<int>)
    T_11 b{ {1, 2, 3}, 4, 5}; // T_11(std::initializer_list<int>, int, int)
    ```

# 기존 생성자와 initializer_list 생성자와의 충돌

[중괄호 초기화 우선 순위](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%9A%B0%EC%84%A0-%EC%88%9C%EC%9C%84)에 따라 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)를 사용한 버전이 비교적 우선적으로 선택됩니다. 그러다 보니 기존 생성자들과 충돌할 수도 있는데요, `vector(size_t count);` 를 호출하기 위해 `vector<int> v_11{2};` 와 같이 한다면, 요소가 2개인 `vector` 를 생성하는게 아니라, `2`값인 요소 1인 `vector`를 생성합니다.

```cpp
// 요소가 2개인 vector를 생성합니다.
std::vector<int> v(2);
EXPECT_TRUE(v.size() == 2 && v[0] == 0 && v[1] == 0);

// 요소값이 2인 vector를 생성합니다.
std::vector<int> v_11{2};
EXPECT_TRUE(v_11.size() == 1 && v_11[0] == 2);   
```    

# (C++17~) 중괄호 초기화에서 auto 추론의 새로운 규칙

기존에는 [auto](https://tango1202.github.io/mordern-cpp/mordern-cpp-auto-decltype/#auto) 와 [중괄호 직접 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-t-t)를 사용하면, [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)로 추론되는 문제가 있다고 합니다.([N3681](https://open-std.org/JTC1/SC22/WG21/docs/papers/2013/n3681.html) 참고)

```cpp
auto x{foo}; // direct-initialization, initializes an initializer_list
```

제가 GCC version 8.1.0로 해보니 `auto b_17{1};` 이 `int`로 잘 추론되기는 합니다만, 아무튼 C++17 부터는 이를 개선하여 다음과 같은 규칙으로 추론됩니다.

* `auto val{}` : 인자가 1개면 인자 타입으로 추론되고, 여러개면 컴파일 오류를 발생합니다.
* `auto val = {}` : 인자들의 타입이 동일하면 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)로 추론됩니다.

```cpp
int a_17{1}; // a는 int
auto b_17{1}; // b는 int
auto c_17 = {1}; // c는 initializer_list<int>
auto d_17 = {1, 2}; // d는 initializer_list<int>  
// auto e_17{1, 2}; // (X) 컴파일 오류. auto에서는 단일 개체 대입 필요  
```


