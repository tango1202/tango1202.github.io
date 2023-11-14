---
layout: single
title: "#8. [모던 C++] (C++11~) 중괄호 초기화, (C++20~) 지명 초기화"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#7] 객체 생성시 괄호와 중괄호를 구분하라.
>   * 중괄호는 [인자의 암시적 형변환 차단](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%9D%B8%EC%9E%90%EC%9D%98-%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98-%EC%B0%A8%EB%8B%A8)해준다.
>   * 괄호는 기본 생성자 호출 지원한다.([기존 생성자와 initializer_list 생성자와의 충돌](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EA%B8%B0%EC%A1%B4-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80-initializer_list-%EC%83%9D%EC%84%B1%EC%9E%90%EC%99%80%EC%9D%98-%EC%B6%A9%EB%8F%8C) 참고)


> * (C++11~) [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)가 추가되어 클래스, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/), 구조체를 일관성 있게 초기화 할 수 있습니다.
> * (C++11~) [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)로 함수 인수 전달, 리턴문 작성을 간소화할 수 있습니다.
> * (C++11~) [중괄호 초기화시 인자의 암시적 형변환을 일부 차단](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%9D%B8%EC%9E%90%EC%9D%98-%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98-%EC%B0%A8%EB%8B%A8)하여, 코딩 계약이 개선되었습니다.
> * (C++11~) [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 가 추가되어 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)등 컨테이너 요소 추가가 간편해 졌습니다.
> * (C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.
> * (C++20~) [지명 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#c20-%EC%A7%80%EB%AA%85-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)시 변수명을 지명하여 값을 초기화 할 수 있습니다.

# 개요

C++11 부터는 개체 생성시 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)를 이용한 방법이 추가되어 일관된 초기화 방법을 제공하고, [초기화 파싱 오류](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98)를 개선했습니다.

다음과 같이 기존 괄호(`()`)를 중괄호(`{}`)로 대체하여 초기화 할 수 있으며 `=` 사용 여부에 따라 [중괄호 직접 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-t-t)와 [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)로 구분됩니다.

```cpp
int a = 10;

int b(10); // 괄호 초기화
int b_11{10}; // 중괄호 직접 초기화
int c_11 = {10}; // 증괄호 복사 초기화
```

# 중괄호 초기화

[초기화 파싱 오류](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98) 에서도 언급했듯 `()`을 사용하는 초기화 방법은 컴파일러가 다르게 해석(*[기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90) 호출을 함수 선언으로 오해*)할 소지가 있습니다. 그리고, 클래스인지, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)인지, 구조체 인지에 따라 때로는 괄호를 생략하거나, 괄호를 넣거나, 중괄호를 사용해야 하거나 뒤죽 박죽입니다.

```cpp
class T {
    int m_A;
    char m_B;
public:
    T() {}
    T(int a, char b) : m_A(a), m_B(b) {}    
};
T obj(); // (△) 비권장. T를 리턴하는 obj 함수 선언

T obj1; // 기본 생성자로 T 개체 생성
T obj2(T()); // (△) 비권장. T 타입의 기본 생성자로 생성한 것을 T obj2에 복사 생성하고 싶지만, T 타입을 리턴하고, T(*)()함수 포인터를 인자로 전달받는 함수 obj2를 선언합니다. 
T obj3 = T(); // T obj(T());와 유사. T()로 기본 생성된 것을 T obj3에 복사 생성. 단 컴파일러 최적화로 1회만 생성될 수 있음
T obj4(10, 'b'); // m_A == 10, m_B == `b`인 T 개체 생성

T arr[] = {T(), T(10, 'b')}; // T 요소 2개인 배열 생성

struct U {
    int m_A;
    char m_B;
};
U objs = {10, 'b'}; // m_A == 10, m_B == `b`인 U 개체 생성
```

C++11 부터는 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)를 제공하여 클래스인지, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)인지, 구조체의 구분없이 `{}`을 이용하여 일관성 있게 초기화 할 수 있으며, [초기화 파싱 오류](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98)도 해결했습니다.

```cpp
class T {
    int m_A;
    char m_B;
public:
    T() {}
    T(int a, char b) : m_A(a), m_B(b) {}    
};

T obj1_11{}; // 기본 생성자로 T 개체 생성
T obj2_11{T{}}; // 기본 생성자인 T()로 생성한 개체를 obj2_11의 복사 생성자로 복사 생성
T obj3_11 = T{}; // T obj2_11{T{}}와 유사. T{}로 기본 생성된 것을 T obj2_11 복사 생성. 단 컴파일러 최적화로 1회만 생성될 수 있음
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

1. 기존에는 생성자 호출시 `T()`와 같이 괄호(`()`)를 사용했는데, 중괄호(`{}`)를 사용합니다. 특히 기본 생성자 호출이 함수 선언으로 인식되는 [초기화 파싱 오류](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%B4%88%EA%B8%B0%ED%99%94-%ED%8C%8C%EC%8B%B1-%EC%98%A4%EB%A5%98)가 말끔히 해결됐습니다.

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

`T t = {};` 표현은 `T t = T{};`의 축약형입니다. [중괄호 직접 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%81%EC%A0%91-%EC%B4%88%EA%B8%B0%ED%99%94-t-t)인 `T{}`로 개체를 생성하고, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 이용하여 `t`을 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)하는 표현입니다.

그래서 [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)가 있고, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 없다면, `T a_11{}`은 되나 `T b_11 = {}`는 안됩니다. `=`하나 차이로요.

```cpp
class T {
public:
    T() {}
    explicit T(int) {}
    T(const T&) = delete; // 복사 생성자를 사용할 수 없습니다.
};       

T a_11{10};
// T b_11 = {10}; // (X) 컴파일 오류. 복사 생성자를 사용합니다.
```

1. 생성자 호출시 사용할 수 있습니다.
   
    이때, `T t = T{};`은 컴파일러 최적화에 의해 `T t{};`와 동일하게 작동할 수 있습니다.(*[생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94) 참고*)

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
    T c_11 = T{}; // T 기본 생성자를 호출하고, 복사 생성자를 호출. 컴파일러 최적화에 의해 2개의 생성을 1개의 생성으로 바꿈
    ```

    모두 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 1회만 호출하는 것을 알 수 있습니다.

    ```cpp
    T : Default Constructor // T a_11{};
    T : Default Constructor // T b_11 = {};
    T : Default Constructor // T c_11 = T{};
    ```
    > *(C++17~) [임시 구체화와 복사 생략 보증](https://tango1202.github.io/mordern-cpp/mordern-cpp-copy-elision/)을 통해 컴파일러 의존적이었던 [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94), [리턴값 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92-%EC%B5%9C%EC%A0%81%ED%99%94return-value-optimization-rvo)등이 표준화 되었습니다.*

    [값 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B0%92-%EC%B4%88%EA%B8%B0%ED%99%94)를 보면, [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)를 이용하여 생성한 개체를 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 이용하여 대입하는 표현이 있습니다.(*사실 컴파일러 최적화에 의해 생성자를 1회 호출하기는 합니다. [생성자 호출 및 함수 인수 전달 최적화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EC%83%9D%EC%84%B1%EC%9E%90-%ED%98%B8%EC%B6%9C-%EB%B0%8F-%ED%95%A8%EC%88%98-%EC%9D%B8%EC%88%98-%EC%A0%84%EB%8B%AC-%EC%B5%9C%EC%A0%81%ED%99%94) 참고*)

    다음처럼요.

    ```cpp
    int a(int(10)); // int(10)으로 생성한 개체를 int a 의 복사 생성자를 호출하여 생성합니다.
    int b = int(10); // int(10)으로 생성한 개체를 int a 의 복사 생성자를 호출하여 생성합니다.
    ```
    요걸 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)로 수정하면 다음과 같습니다.

    ```cpp
    int a_11{int{10}};
    int b_11 = int{10};
    ```

    [중괄호 복사 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EB%B3%B5%EC%82%AC-%EC%B4%88%EA%B8%B0%ED%99%94-t-t---t---f-return-)의 축약 표현을 사용해서 `int{10}`을 `{10}`으로 바꿔보면 [기본 타입](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-type/)은 [중괄호 초기화 중첩](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%A4%91%EC%B2%A9)을 지원하지 않는다며 컴파일 오류가 납니다.

    ```cpp
    // int c_11{ // (X) 컴파일 오류. 기본 타입은 중괄호 중첩을 지원하지 않습니다. 
    //    {10}
    // };
    int d_11 = {10};
    ``` 

    하지만, 개체 타입이면 가능합니다.

    ```cpp
    class T {
    public:
        explicit T(int) {}    
    };

    T a_11{T{10}}; // (O)
    T b_11 = T{10}; // (O)

    // 축약형
    T a_11{ // (O)
        {10} // T{10}
    }; 
    T b_11 = T{10}; // (O)              
    ```

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

[집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)의 경우에는 기존에도 `= {}` 표현을 지원했는데요(*[배열 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EB%B0%B0%EC%97%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 와 [구조체 초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/#%EA%B5%AC%EC%A1%B0%EC%B2%B4-%EC%B4%88%EA%B8%B0%ED%99%94) 참고*),

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
> *(C++20~) [지명 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#c20-%EC%A7%80%EB%AA%85-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)시 변수명을 지명하여 값을 초기화 할 수 있습니다.* 

# 인자의 암시적 형변환 차단

[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)는 생성자 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 기존보다는 좀더 차단해 줍니다. 코딩 계약이 좀더 단단해 졌지만, [사용자 정의 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)은 여전히 막지 못합니다.

1. 실수에서 정수로 변환을 차단합니다.

    `T a(3.14);`은 `double`형이 `int`로 [암시적으로 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)되어 호출됩니다만([오버로딩된 함수 결정 규칙](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99) 참고), `T b{3.14};` 은 컴파일 오류를 발생시킵니다.

    ```cpp
    class T {
    public:
        explicit T(int) {};
    };

    T a(3.14); // (△) 비권장. double 이 int로 암시적으로 변환되어 초기화 됩니다.
    T b_11{3.14}; // (X) 컴파일 오류. 암시적 변환을 차단합니다.
    ```

2. `double`에서 `float`으로의 변환을 경고합니다. 단, 상수 표현식에서 해당 값을 저장할 수 없다면 컴파일 오류이고, 해당 값을 저장할 수 있다면 허용합니다.(*`int`에서 `char` 변환도 동일합니다.*)

    ```cpp
    class T {
    public:
        explicit T(float) {};
    };  

    T a_11{3.14}; // 상수 표현식은 값 범위라면 허용. 3.14는 float이 저장할 수 있어서 허용

    double doubleVal = 3.14;   
    T b_11{doubldVal}; // (X) 컴파일 경고. double을 float으로 변환하는건 경고합니다.
    ```

3. `int`에서 `char` 변환도 `double`에서 `float`의 변환과 동일합니다.

    ```cpp
    class T {
    public:
        explicit T(char) {};
    }; 
    
    T a_11{10}; // 상수 표현식은 값 범위라면 허용. 10은 char에서 저장할 수 있어서 허옹
    T b_11{255}; // (X) 컴파일 오류. 255는 char에서 저장할 수 없음. 

    int intVal = 10;  
    T c_11{intVal}; // (X) 컴파일 경고. int를 char로 변환하는건 경고합니다.
    ```

4. 포인터 타입에서 [bool](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-bool/)로의 변환을 경고해 줍니다.

    ```cpp
    int* ptr;
    bool b(ptr);
    bool b_11{ptr}; // (X) 컴파일 경고. int*에서 bool로 변환하는건 경고합니다.
    ```
   
5. 사용자가 [형변환 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90) 를 작성하면 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)이 허용됩니다.
   
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

[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 초기화 할때 다음처럼 [중괄호로 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/) 할 수 있는데요,

```cpp
class A {
    int m_X;
    int m_Y;
public:
    A(int x, int y) : m_X{x}, m_Y{y} {}
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
    A(int x, int y) : m_X{x}, m_Y{y} {}
};
class B {
    int m_Val;
    A m_A;
public:
    B(int val, A a) : m_Val{val}, m_A{a} {}
};

B b_11{1, {2, 3}}; // B b_11{1, A{2, 3}};와 동일
```

멤버 변수가 `public`인 [집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)이라면, [값 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B0%92-%EC%83%9D%EC%84%B1%EC%9E%90)가 없어도 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)이나 구조체처럼 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)로 초기화할 수 있습니다.

```cpp
class A {
public:
    int m_X; // 내부 멤버가 public인 집합 타입이면 값 생성자가 없어도 됩니다.
    int m_Y;
};
class B {
public:
    int m_Val; // 내부 멤버가 public인 집합 타입ㄴ이면 값 생성자가 없어도 됩니다.
    A m_A;
};

B b_11{1, {2, 3}}; // B b_11 = B{1, A{2, 3}};와 동일
```

# initializer_list

[initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 가 추가되어 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)등 컨테이너 요소 추가가 간편해 졌습니다. 
 
 C++11의 컨테이너는 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)를 이용하여 쉽게 초기값을 입력할 수 있도록 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 을 사용한 생성자를 제공합니다.

[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)의 경우 요소를 입력할때 `push_back()`을 사용하여 코드 작성이 번거로웠는데(*[vector 의 삽입과 삭제](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container-insert-erase/#vector-%EC%9D%98-%EC%82%BD%EC%9E%85%EA%B3%BC-%EC%82%AD%EC%A0%9C) 참고*), [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 을 사용한 생성자를 이용하면 다음처럼 좀 더 간편하게 요소 값을 초기화 할 수 있습니다.

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
|`begin()` (C++11~)|첫번째 요소의 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/) 포인터를 리턴합니다.|
|`end()` (C++11~)|마지막 요소의 다음 위치의 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/) 포인터를 리턴합니다.|

# initializer_list의 암시적 생성

다음의 경우 [중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/) 표현식은 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)를 암시적으로 자동 생성합니다.

1. 생성자나 함수의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 인 경우

    ```cpp
    class T_11 {
    public:
        explicit T_11(std::initializer_list<int>) {}
    };
    T_11 t{
        {1, 2, 3} // {1, 2, 3} 은 initializer_list를 생성해서 전달합니다.
    }; 
    ```

2. [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)가 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 인 경우

    ```cpp
    class T_11 {
    public:
        void operator =(std::initializer_list<int> data_11) { // 복사 대입 연산자가 initializer_list를 전달받습니다.
            std::vector<int> v;
            const int* itr = data_11.begin();
            const int* endItr = data_11.end();
            for (;itr != endItr; ++itr) {
                v.push_back(*itr);
            }
        }    
    };
    T_11 t;
    t = {1, 2, 3}; // {1, 2, 3} 은 initializer_list를 생성해서 전달합니다.  
    ```

3. [범위 기반 for()](https://tango1202.github.io/mordern-cpp/mordern-cpp-statements/#%EB%B2%94%EC%9C%84-%EA%B8%B0%EB%B0%98-for)에서 범위 표현식으로 사용하는 경우
   
   ```cpp
    std::vector<int> v;
    for (int a_11 : {1, 2, 3}) { // {1, 2, 3} 은 initializer_list를 생성하고 범위 기반 for문에서 사용됩니다.
        v.push_back(a_11);
    }  
   ```

# 중괄호 초기화 우선 순위

[중괄호 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/)는 기존 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)와의 호환성과 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list) 의 편의성을 위해 다음의 우선 순위에 맞춰 동작합니다.

1. 문자 [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)에서 [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)로 초기화 합니다.

    ```cpp
    char arr_11[] = {"abc"}; // char arr_11[] = "abc"; 와 동일
    ```
2. [집합 타입](https://tango1202.github.io/mordern-cpp/mordern--category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)인 경우 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)를 합니다.

3. 중괄호가 비어 있고 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)가 있으면, [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.

4. [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)를 사용한 생성자가 있으면 해당 생성자를 호출합니다. 

    ```cpp
    class T_11 {
    public:
        T_11(int, int, int, int, int) {}
        T_11(std::initializer_list<int>) {}
        T_11(std::initializer_list<int>, int, int) {}
    };
    T_11 a{1, 2, 3, 4, 5}; // T_11(std::initializer_list<int>)
    T_11 b{ 
        {1, 2, 3}, 4, 5 // T_11(std::initializer_list<int>, int, int)
    }; 
    ```

# 기존 생성자와 initializer_list 생성자와의 충돌

[중괄호 초기화 우선 순위](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%9A%B0%EC%84%A0-%EC%88%9C%EC%9C%84) 3번에 따라 다음처럼 해석됩니다.

```cpp
// std::vector<int> v1(); // 함수 선언
std::vector<int> v1_11{}; // 기본 생성자

std::vector<int> v2_11{
    {} // 빈 initializer_list로 vector 생성
};
```

상기는 별로 심각하지 않을 수 있지만, [중괄호 초기화 우선 순위](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%9A%B0%EC%84%A0-%EC%88%9C%EC%9C%84) 4번인 [initializer_list](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#initializer_list)를 사용한 버전이 비교적 우선적으로 선택되는 부분은 심각합니다. 

기존 생성자들과 충돌할 수도 있는데요, `vector(size_t count);` 를 호출하기 위해 `vector<int> v_11{2};` 와 같이 한다면, 요소가 2개인 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/) 를 생성하는게 아니라, `2`값인 요소 하나만 있는  [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)를 생성합니다. 따라서 요소 갯수가 2개인  [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)를 생성하려면 `v3(2)`와 같이 `()`를 사용해야 합니다.

```cpp
std::vector<int> v1(); // 함수 선언
std::vector<int> v1_11{}; // 기본 생성자

std::vector<int> v2_11{
    {} // 빈 initializer_list로 vector 생성
}; 

std::vector<int> v3(2); // 요소 갯수가 2개인 vector 생성
EXPECT_TRUE(v3.size() == 2 && v3[0] == 0 && v3[1] == 0);

std::vector<int> v3_11{2}; // 요소값이 2인 vector 생성
EXPECT_TRUE(v3_11.size() == 1 && v3_11[0] == 2);  
```

# (C++20~) 지명 초기화

기존의 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)는 중괄호 내에서 선언 순서대로 나열해서 초기화 했습니다. 만약 멤버 변수가 많다면 어느것을 초기화 하는지 헷갈릴 수가 있는데요,

```cpp
class T_11 {
public:
    int a, b, c, d, e;
};

T_11 obj{1, 2, 3, 4, 5}; // 갯수가 많아지면 어느값이 어느 멤버 변수를 초기화 하는지 헷갈립니다.
```

C++20 부터는 [지명 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#c20-%EC%A7%80%EB%AA%85-%EC%B4%88%EA%B8%B0%ED%99%94)가 추가되어 [중괄호 집합 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%A7%91%ED%95%A9-%EC%B4%88%EA%B8%B0%ED%99%94)시 변수명을 지명하여 값을 초기화 할 수 있습니다.

다음과 같은 특징이 있습니다.

1. 멤버 변수 선언과 초기화 순서가 동일해야 합니다.
2. 특정 항목을 생략할 수 있습니다. 생략된 것은 기본 생성자로 초기화됩니다.
3. [지명 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#c20-%EC%A7%80%EB%AA%85-%EC%B4%88%EA%B8%B0%ED%99%94)에서는 [초기화 중첩](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%A4%91%EC%B2%A9)을 지원하지 않습니다.
4. [인자의 암시적 형변환 차단](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%9D%B8%EC%9E%90%EC%9D%98-%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98-%EC%B0%A8%EB%8B%A8)과 마찬가지로 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 평가합니다.
    1. 실수에서 정수로 변환을 차단하며,
    2. `double`에서 `float` 변환은 경고 합니다. 단, 상수 표현식에서 해당 값을 저장할 수 없으면 오류이고, 해당 값을 저장할 수 있다면 허용합니다.
    3. *`int`에서 `char` 변환도 `double`에서 `float` 변환과 동일합니다.
    4. 포인터 타입에서 [bool](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-bool/)로의 변환을 경고해 줍니다.
    5. 사용자가 [형변환 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%98%95%EB%B3%80%ED%99%98-%EC%83%9D%EC%84%B1%EC%9E%90) 를 작성하면 [암시적 형변환](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)이 허용됩니다.
5. [집합 타입](https://tango1202.github.io/mordern-cpp/mordern-cpp-type-category/#%EC%A7%91%ED%95%A9-%ED%83%80%EC%9E%85)만 지원합니다.
5. [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 지원하지 않습니다.

```cpp
class A {};
class B {
public:
    B(A) {} // explicit가 없는 형변환 생성자. A로 암시적으로 생성됩니다.
};
class Inner {
public:
    int m_Val1;
    int m_Val2;
};

class T {
public:
    int m_X{1};
    int m_Y{2};
    float m_Z{3}; // float 입니다.
    Inner m_Inner;

    bool m_Bool;
    B m_B{A{}}; // A 기본 생성자로 생성합니다.
};

T a_20{.m_X = 10, .m_Y = 20, .m_Z = 30.F};
// T b_20{.m_X = 10, .m_Z = 30.F, .m_Y = 20}; // (X) 컴파일 오류. 멤버 변수 선언 순서와 같아야 합니다.
T c_20{.m_X = 10, .m_Z = 30.F}; // 특정 항목을 생략할 수 있습니다.
EXPECT_TRUE(c_20.m_Y == 2); // 생략한 멤버는 멤버 선언부 초기화 값이 유지됩니다.

// 중첩 초기화
T d_20{10, 20, 30.F, {1, 2}}; // {1, 2}는 Inner 개체를 초기화 합니다.
// T e_20{.m_X = 10, .m_Y = 20, .m_Z = 30.F, m_Inner = Inner{1, 2}}; // (X) 컴파일 오류. 지명 초기화는 초기화 중첩을 지원하지 않습니다.

// 인자의 암시적 형변환 차단 확인
// T f_20{.m_X = 1.5}; // (X) 컴파일 오류. 실수를 정수로 변환시 컴파일 오류가 발생합니다.

T g_20{.m_Z = 3.14}; // 상수값을 저장할 수 있다면 허용합니다.
double h{3.14};
// T i_20{.m_Z = h}; // (X) 컴파일 경고. double을 float으로 변환하는건 경고합니다.

int* ptr;
// T j_20{.m_Bool = ptr}; // (X) 컴파일 경고. 포인터 타입에서 bool 변환하는건 경고합니다.

T k_20{.m_B = A{}}; // (△) 비권장. A->B로의 암시적 변환을 허용하면 차단되지 않습니다.

class U {
public:
    int m_Val;
    U() {}; // 생성자가 있어서 집합 타입이 아닙니다.
};
// U l_20{.m_Val = 0}; // (X) 컴파일 오류. 집합 타입이 아니면 지원하지 않습니다.

// int arr_20[3]{[1] = 1}; // (X) 컴파일 오류. 배열은 지원하지 않습니다.
```

한편, C언어에서는 선언 순서와 다르게 지정할 수도 있고, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 지원하고, [초기화 중첩](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#%EC%A4%91%EA%B4%84%ED%98%B8-%EC%B4%88%EA%B8%B0%ED%99%94-%EC%A4%91%EC%B2%A9)을 지원하며, [지명 초기화](https://tango1202.github.io/mordern-cpp/mordern-cpp-uniform-initialization/#c20-%EC%A7%80%EB%AA%85-%EC%B4%88%EA%B8%B0%ED%99%94)와 비지명 초기화를 혼합해서 사용할 수 있습니다. 

훨씬 자유도가 높은데요, C언어와 C++가 다른 이유는 C++에서는 멤버가 생성의 역순으로 소멸되어야 하고, 초기치는 정확한 순서를 유지해야 하기 때문에 순서 불일치나, 중첩과 혼합을 허용하지 않으며, [배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)은 [람다 표현식](https://tango1202.github.io/mordern-cpp/mordern-cpp-lambda/#%EB%9E%8C%EB%8B%A4-%ED%91%9C%ED%98%84%EC%8B%9D)과 충돌되기 때문에 허용하지 않는다고 합니다.

```cpp
struct A { 
    int m_X;
    int m_Y; 
};
struct B { 
    A m_A; 
};

A a_20 = {.m_Y = 1, .m_X = 2}; // valid C, invalid C++ (순서 불일치)
B b_20 = {.m_A.m_X = 0}; // valid C, invalid C++ (중첩)
A a_20 = {.m_X = 1, 2}; // valid C, invalid C++ (혼합)
int arr_20[3] = {[1] = 5}; // valid C, invalid C++ (배열)   
```