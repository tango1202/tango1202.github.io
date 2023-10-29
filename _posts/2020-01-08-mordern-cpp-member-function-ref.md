---
layout: single
title: "#8. [모던 C++] (C++11~) 멤버 함수 참조 지정자"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)가 추가되어 멤버 함수에 `&`, `&&` 로 좌측값에서 호출될때와 우측값에서 호출될 때를 구분하여 오버로딩 할 수 있습니다.

# 개요

[멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)가 추가되어 멤버 함수에 `&`, `&&` 로 좌측값에서 호출될때와 우측값에서 호출될 때를 구분하여 오버로딩 할 수 있습니다.

```cpp
class T {
public:
    int Func_11() & {return 1;} // #1. 좌측값에서 호출
    int Func_11() && {return 2;} // #2. 우측값에서 호출
};

T t;
EXPECT_TRUE(t.Func_11() == 1); // 좌측값이므로 #1 호출
EXPECT_TRUE(std::move(t).Func_11() == 2); // move는 우측값이므로 #2 호출
EXPECT_TRUE(T().Func_11() == 2); // T() 는 임시 개체(우측값)이므로 #2 호출   
```

# 이동 연산을 지원하는 Wrapper 설계

이동 연산을 지원하기 위해 이동 생성자를 구현한 `Big_11` 개체가 있다고 합시다.

```cpp
class Big_11 {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big_11(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {}

    // 소멸자
    ~Big_11() {delete[] m_Ptr;}

    // 복사 생성자
    Big_11(const Big_11& other) :
        m_Size(other.m_Size) {

        std::cout << "Big_11 : Copy Constructor" << std::endl;

        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }
    // 이동 생성자
    Big_11(Big_11&& other) : 
        m_Size(other.m_Size),
        m_Ptr(other.m_Ptr) {

        std::cout << "Big_11 : Move Constructor" << std::endl;

        other.m_Size = 0; // other는 초기화 합니다.
        other.m_Ptr = nullptr;
    }  
    
    size_t GetSize() const {return m_Size;}
};    
```

이를 관리하는 레퍼는 다음과 같이 만들 수 있습니다.

```cpp
class A_11 {
    Big_11 m_Data;
public:
    A_11() : m_Data(10) {}
    const Big_11& GetData() const {
        return m_Data;
    }
};
```

`GetData()`를 호출하는 곳에서 복사 생성하지 않도록 `const Big_11&`를 리턴했습니다. 아주 좋네요.

```cpp
A_11 a;
const Big_11& big = a.GetData();
```

이를 복사해서 사용하고 싶다면 다음과 같이 하면 됩니다.

```cpp
// a는 좌측값. Big_11의 복사 생성자 호출
A_11 a;
Big_11 big = a.GetData(); 
```

만약 `a`가 이제 버려질 값이라면, 복사 부하를 줄이기 위해 이동 생성자를 호출하는게 좋습니다. 하지만 다음과 같이 `A_11` 개체를 우측값으로 만들어 봤자, `GetData()`는 `const Big_11&`인 좌측값 참조를 리턴하므로, `Big_11`의 이동 생성자는 호출되지 않습니다. 

```cpp
// a은 좌측값. std::move(a_11)는 우측값. Big_11의 복사 생성자 호출
A_11 a;
Big_11 big = std::move(a).GetData();  

// A_11()은 임시 개체인 우측값. Big_11의 복사 생성자 호출
Big_11 big = A_11().GetData(); 
```

그러면 다음은 될까요? [리턴값](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)을 `move()`했지만, `const Big&`를 `const Big&&`를 바꾸기 때문에, 이동 생성자와 [인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 타입이 달라 (*이동 생성자는 `Big_11(Big_11&& other)`입니다. [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/)가 다르죠.*) 그냥 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.

```cpp
// A_11().GetData()는 const Big_11&를 리턴하고, move() 는 const Big_11&& 을 리턴. 이동 생성자 인자 타입과 다르므로, 그냥 복사 생성자 호출 
Big_11 big = std::move(A_11().GetData()); 
```

다음처럼 `const_cast`로 상수성을 버려야 이동 생성을 할 수 있습니다.

```cpp
// Big_11의 이동 생성자 호출
Big_11 big = std::move(const_cast<Big_11&>(A_11().GetData())); 
```

참 실수하기 쉽고, 타이핑도 번거롭죠.

[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/) 에 언급되었듯, **잘못 사용하기엔 어렵게, 바르게 사용하기엔 쉽게** 구현하기 위해 [멤버 함수 참조 지정자](https://tango1202.github.io/mordern-cpp/mordern-cpp-member-function-ref/)를 이용하면 다음처럼 레퍼를 작성할 수 있습니다.

```cpp
class A_11 {
    Big_11 m_Data;
public:
    A_11() : m_Data(10) {}
    const Big_11& GetData() const & { // #1
        return m_Data;
    }

    Big_11&& GetData() && {
        return std::move(m_Data); // #2
    }
};
```

기존 `GetData()` 함수에 `&`을 붙였고, `GetData() &&` 오버로딩 버전이 추가되었습니다. 이제 `A_11`이 임시 개체(우측값) 일때는 알아서 `GetData() &&` 버전을 호출하므로, 사용하기 훨씬 수월합니다.

```cpp
// a은 좌측값. #1이 호출되어 const Big_11&을 리턴하고, Big_11의 복사 생성자 호출
A_11 a;
Big_11 big = a.GetData(); 

// a_11은 좌측값. std::move(a_11)는 우측값. #2가 호출되어 Big_11&&을 리턴하고, Big_11의 이동 생성자 호출
A_11 a;
Big_11 big = std::move(a).GetData();            

// A_11()은 임시 개체인 우측값. #2가 호출되어 Big_11&&을 리턴하고, Big_11의 이동 생성자 호출
Big_11 big = A_11().GetData(); 
```

