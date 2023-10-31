---
layout: single
title: "#10. [모던 C++] (C++11~) 전달 참조"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#23] std::move와 std::forward를 숙지하라.

> * [MEC++#24] 전달 참조와 우측값 참조를 구별하라.([전달 참조](??))

# 전달 참조

[전달 참조](??)는 [우측값 참조](??)나 [좌측값 참조](??)를 할 수 있는 특수한 [참조자](??)입니다.

`A&&`의 형태로 [우측값 참조](??)를 전달 받을 수 있는데요,

```cpp
A val;
A&& ref_11 = std::move(val);
```

특별히 [auto](??)와 [템플릿 인자](??)에 다음처럼 `auto&&`과 `T&&`로 형태로 사용하면 [전달 참조(forwardinng reference)](??)가 됩니다.

```cpp
A val;
auto&& a_11 = val; // auto 추론을 합니다.

template<typename T>
void Func(T&& param) { // 템플릿 함수 인수 추론을 합니다.
    ...
}
```

[전달 참조](??)는 

1. [우측값 참조](??), [좌측값 참조](??)를 모두 전달받을 수 있습니다.

    다음 예에서 우측값과 좌측값을 모두 전달받는걸 알 수 있습니다.(*`rref_11`은 `T&&`로 선언했지만, 이름이 있기 때문에 좌측값입니다.([이름이 부여된 우측값](??) 참고) 따라서, `c_11`은 [좌측값 참조](??)인 `T&`입니다.*)

    ```cpp
    class T {};
    T val;
    T& ref = val;
    T&& rref_11 = static_cast<T&&>(val);   

    auto&& a_11 = val; // T&. val는 좌측값. 이를 참조로 만들면 T&
    auto&& b_11 = ref; // T&. ref는 좌측값 참조
    auto&& c_11 = rref_11; // T&. rref_11는 이름이 있으니 좌측값
    auto&& d_11 = std::move(val); // T&& 
    ```

2. [상수](??), 비상수를 모두 전달받을 수 있습니다.

    ```cpp
    class T {};
    const T val;
    const T& ref = val;
    const T&& rref_11 = static_cast<const T&&>(val);   

    auto&& a_11 = val; // const T&. val는 좌측값. 이를 참조로 만들면 T&
    auto&& b_11 = ref; // const T&. ref는 좌측값 참조
    auto&& c_11 = rref_11; // const T&. rref_11는 이름이 있으니 좌측값
    auto&& d_11 = std::move(val); // const T&& 
    ```

3. 만약 [전달 참조](??)가 아니라면 [우측값 참조](??)만 대입받을 수 있으므로, [좌측값 참조](??) 를 전달했을때 컴파일 오류가 납니다.

    ```cpp
        // auto&& 가 아닌 형태로 만들기 위해 억지로 const를 넣었습니다.
        // const auto&& e_11 = ref; // (X) 컴파일 오류. 우측값 참조에 좌측값을 대입할 수 없음
        const auto&& f_11 = std::move(val); // const T&& 
    ```

 [전달 참조](??)를 만드려면 다음 조건을 만족해야 합니다.

 1. 정확히 `auto&&`과 `T&&` 이어야 합니다. `const auto&&`나 `const T&&`는 [전달 참조](??)가 아닙니다.

 2. 반드시 타입 추론이 되야 합니다.
 
    다음 예에서 템플릿 `A`의 `f()`함수는 인스턴스화 된 뒤에는 `f(int&& val)`로 구체화될 수 있기 때문에 [전달 참조](??) 라고 확신하기는 어렵습니다.

    하지만 템플릿 `A`의 `g()`함수는 함수 호출 시점에 전달한 타입으로 추론하기 때문에 [전달 참조](??)입니다.
    ```cpp
    auto&& a_11 = val; // val 타입으로 추론합니다.

    template<typename T>
    void f(T&& val) {} // val 타입으로 추론합니다.

    template<typename T> 
    class A {
    public:
        void f(T&& val) {} // A<int> val; 와 같이 인스턴스화 된 뒤에는 f(int&& val)로 구체화될 수 있기 때문에 val 타입으로 추론된다고 확신할 수 없습니다. 

        template<typename U> 
        void g(U&& val) {} // val 타입으로 추론합니다.
    }; 

# 참조 축약


참조자의 참조는 불법입니다.

템플릿 인수 추론에서 참조성은 제거합니다.

다음처럼 억지로 참조자로 만들 수 있습니다.





참조 축약을 위해 만들어 보면

```cpp
template<typename T>
class Convert_11 {
public:
    using LRef = T&; // T 타입에 좌측값 참조를 더합니다.
    using RRef = T&&; // T 타입에 우측값 참조를 더합니다.
};
```

1. 기본 형태

```cpp
class T {};
T obj;
Convert_11<T>::LRef a = obj; // T&
Convert_11<T>::RRef b = std::move(obj); // T&& 
```

2. & + & 과 & + &&

```cpp
class T {};
T obj;
Convert_11<T&>::LRef a = obj; // T&, & + &
Convert_11<T&>::RRef b = obj; // T&, & + &&
```

3. && + & 과 && + &&
```cpp
class T {};
T obj;
Convert_11<T&&>::LRef a = obj; // T&, && + &
Convert_11<T&&>::RRef b = std::move(obj);; // T&&, && + &&  
```

즉 다음과 같이 참조 축약을 합니다.


# move() 원리

`move()`함수는 STL에 다음처럼 구현됩니다.

1. `remove_reference`는 좌측값이던, 우측값이던 [참조성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)을 제거한 타입입니다.
2. 참조성을 제거한 것에 `&&`을 붙여 캐스팅하여 리턴합니다.

먼저 참조성을 제거한 것 말고는 `static_cast<T&&>`와 같죠.

```cpp
template<typename T>
typename remove_reference<T>::&& move(T&& param) {
    using ReturnType = typname remove_reference<T>::type&&;

    return static_cast<ReturnType>(param);
}
```

```cpp
template<typename T>
T&& MyMove(T& val) {
    using ReturnType = T&;
    auto T
    return static_cast<ReturnType&&>
}

class T{
public:
    int Func(T&) {return 1;} // #1
    int Func(T&&) {return 1;} // #2
};
T a;

T& b = a;
EXPECT_TRUE(a.Func(MyMove(b)));
```


`move()`함수는 `T`와 `T&`를 단순히 `&&`로 변환해 주는 함수이므로 `const T` 를 전달하면 `const T&&`로 변경합니다. 그런데, 이동 생성자, 이동 대입 연산자는 `T&&`를 사용하므로 `const T&&`로 변환하면 호출되지 않습니다. 따라서 [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)를 제거하고 사용해야 합니다.(*[이동 연산을 지원하는 래퍼](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%84-%EC%A7%80%EC%9B%90%ED%95%98%EB%8A%94-%EB%9E%98%ED%8D%BC) 참고*)