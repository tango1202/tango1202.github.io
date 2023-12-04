---
layout: single
title: "#12. [모던 C++] (C++11~) 멤버 함수 참조 지정자"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)가 추가되어 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)에 `&`, `&&` 로 [좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)에서 호출될때와 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)에서 호출될 때를 구분하여 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 할 수 있습니다.

# 개요

[멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)가 추가되어 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)에 `&`, `&&` 로 [좌측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)에서 호출될때와 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)에서 호출될 때를 구분하여 [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)을 할 수 있습니다.

```cpp
class T {
public:
    int Func_11() & {return 1;} // #1. 좌측값에서 호출
    int Func_11() && {return 2;} // #2. 우측값에서 호출
};

T t;
EXPECT_TRUE(t.Func_11() == 1); // 좌측값이므로 #1 호출
EXPECT_TRUE(std::move(t).Func_11() == 2); // move는 우측값이므로 #2 호출
EXPECT_TRUE(T{}.Func_11() == 2); // T{} 는 임시 개체(우측값)이므로 #2 호출   
```

# 이동 연산을 지원하는 래퍼

이동 연산을 지원하기 위해 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 구현한 `Big_11` 개체가 있다고 합시다.

```cpp
class Big_11 {
public:    
    Big_11() {}
    ~Big_11() {}

    // 복사 생성자
    Big_11(const Big_11& other) {
        std::cout << "Big_11 : Copy Constructor" << std::endl;
    }
    // 이동 생성자
    Big_11(Big_11&& other) noexcept {
        std::cout << "Big_11 : Move Constructor" << std::endl;
    }  
};    
```

이를 관리하는 래퍼는 다음과 같이 만들 수 있습니다.

```cpp
class Wrapper_11 {
    Big_11 m_Data;
public:
    Wrapper_11() : m_Data{} {}
    const Big_11& GetData() const {
        return m_Data;
    }
};
```

`GetData()`를 호출하는 곳에서 쓸데없이 [복사 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)하지 않도록 `const Big_11&`를 리턴했습니다.

```cpp
Wrapper_11 wrapper;
const Big_11& big{wrapper.GetData()}; // const Big_11&을 리턴하므로 참조만 합니다.
```

이를 복사해서 사용하고 싶다면 다음과 같이 하면 됩니다.

```cpp
Wrapper_11 wrapper;
Big_11 big{wrapper.GetData()}; // const Big_11& 를 리턴한 것을 Big_11로 받으므로 복사 생성 합니다.
```

만약 `wrapper`가 이제 버려질 값이라면, 복사 부하를 줄이기 위해 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출하는게 좋습니다. 하지만 다음과 같이 `wrapper` 개체를 [우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)으로 만들어 봤자, `GetData()`는 `const Big_11&`인 [좌측값 참조](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)를 리턴하므로, `Big_11`의 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)는 호출되지 않습니다. 

```cpp
// std::move(wrapper)로 우측값으로 만들어 봤자, std::move(a).GetData()는 const Big_11&(좌측값 참조)를 리턴
Wrapper_11 wrapper;
Big_11 big{std::move(wrapper).GetData()}; // 복사 생성자를 호출합니다.

// Wrapper_11()은 임시 개체인 우측값 이지만, 여전히 GetData()는 const Big_11&(좌측값 참조)를 리턴
Big_11 big{Wrapper_11{}.GetData()}; // 복사 생성자를 호출합니다.
```

그러면 다음은 될까요? [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)했지만, `const Big_11&`를 `const Big_11&&`로 바꾸기 때문에(*[상수 개체의 move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-forwarding-reference/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4%EC%9D%98-move) 참고*), [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)와 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입이 달라 (*[이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)는 `Big_11(Big_11&& other)`입니다. [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)가 다르죠.*) 그냥 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.

```cpp
// Wrapper_11().GetData()는 const Big_11&를 리턴하고, move()는 const Big_11&& 을 리턴. 이동 생성자와 인자 타입과 다르므로, 그냥 복사 생성자 호출 
Big_11 big{
    std::move(
        Wrapper_11{}.GetData()
    )
}; 
```

다음처럼 [const_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)로 [상수성](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)을 버려야 [이동 생성](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)을 할 수 있습니다.

```cpp
// Big_11의 이동 생성자 호출
Big_11 big{ // 3. 이동 생성자 호출
    std::move( // 2. Big&&로 변환하여
        const_cast<Big_11&>( // 1. 상수성을 떼고
            Wrapper_11{}.GetData()
        )
    )
}; 
```

되기는 합니다만, 참 실수하기 쉽고, 타이핑도 번거롭고 [const_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)는 [상수성 계약](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98%EC%84%B1-%EA%B3%84%EC%95%BD)을 바꾸는 거라 싫습니다.

[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)에 언급되었듯, **잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게** 구현할 필요가 있는데요, [멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)가 이럴때 딱 좋습니다.

```cpp
class Wrapper_11 {
    Big_11 m_Data;
public:
    Wrapper_11() : m_Data() {}
    const Big_11& GetData() const & { // #1. Wrapper_11가 좌측값일때 호출됩니다.
        return m_Data;
    }

    Big_11&& GetData() && {
        return std::move(m_Data); // #2. Wrapper_11가 우측값일때 호출됩니다.
    }
};
```

기존 `GetData()` 함수에 `&`을 붙였고, `GetData() &&` [함수 오버로딩](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9) 버전을 추가하였습니다. 이제 `Wrapper_11`이 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(*[우측값](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%A2%8C%EC%B8%A1%EA%B0%92lvalue-left-value%EA%B3%BC-%EC%9A%B0%EC%B8%A1%EA%B0%92rvalue-right-value)*) 일때는 알아서 `GetData() &&` 버전을 호출하므로, 사용하기 훨씬 수월합니다.

```cpp
// wrapper은 좌측값. #1이 호출되어 const Big_11&을 리턴하고, Big_11의 복사 생성자 호출
Wrapper_11 wrapper;
Big_11 big = wrapper.GetData(); 

// wrapper은 좌측값. std::move(wrapper)는 우측값. #2가 호출되어 Big_11&&을 리턴하고, Big_11의 이동 생성자 호출
Wrapper_11 wrapper;
Big_11 big = std::move(wrapper).GetData();            

// Wrapper_11{}은 임시 개체인 우측값. #2가 호출되어 Big_11&&을 리턴하고, Big_11의 이동 생성자 호출
Big_11 big = Wrapper_11().GetData(); 
```