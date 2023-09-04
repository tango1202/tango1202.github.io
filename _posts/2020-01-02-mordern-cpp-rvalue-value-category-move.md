---
layout: single
title: "#2. [모던 C++] (C++11~) 우측값과 값 카테고리, 이동 연산, 이동 생성자, 이동 대입 연산자, move, forward"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

C++의 STL을 보면 대부분 값 기반 복사/대입이 이루어져 속도 성능이 저하될 수 있습니다. 이런 문제를 해결하고자 C++11 부터 `move()` 연산을 지원하게 되었으며, 우측값과 값 카테고리에 대한 이해가 있어야 이를 활용할 수 있습니다. 포인터나 참조자도 어려웠습니다만, 우측값과 값 카테고리는 더욱더 어렵습니다. 하지만 이를 이해하지 않고는 Mordern C++를 활용할 수 없기에 꼭 이해해야만 합니다.

우측값의 컨셉은 다음과 같습니다.

1. 오른쪽 표현식(`a + b`)의 결과 값은 임시 변수임.
2. 임시 변수는 어짜피 버려질 것이므로 굳이 `c`에 복사하지 말고 이동시키면 속도가 조금이라도 빨라짐.

```cpp
int a, b; // a, b 는 좌측값
int c = a + b; // c는 좌측값. a + b 연산 결과는 임시 변수로서 우측값
```

# 왼쪽값(lvalue, left Value)과 우측값(rvalue, right value)

C에서의 정의는 다음과 같습니다.

|항목|내용|
|--|--|
|좌측값|변수처럼 대입시에 왼쪽 또는 오른쪽에 올 수 있는 표현식|
|우측값|일련의 수식처럼 대입시에 왼쪽에만 올 수 있는 표현식|

C++의 정의는 좀더 구체적으로 다음과 같습니다.

|항목|내용|
|--|--|
|좌측값|변수처럼 어떠한 메모리 위치를 가리키며 `&`로 그 위치를 참조할 수 있는 표현식|
|우측값|좌측값이 아닌 값. 즉 위치를 참조할 수 없는 임시 변수나 상수값|

다음처럼 `&`연산이 불가능하면 우측값입니다.

```cpp
int a = 0; 
int* ptr1 = &a; // a는 &로 위치 참조가 되므로 좌측값
int* ptr2 = &0; // (X) 컴파일 오류. 상수 0은 & 위치 참조가 안되므로 우측값
int b = &(a + 10); // (X) 컴파일 오류. 수식 (a + 10)은 & 위치 참조가 안되므로 우측값
```

혹은 참조자로 저장할 수 없으면 우측값입니다.

```cpp
int a = 0; 
int& ptr1 = a; // a는 &로 참조되므로 좌측값
int& ptr2 = 0; // (X) 컴파일 오류. 상수 0은 &로 참조가 안되므로 우측값
int& b = (a + 10); // (X) 컴파일 오류. 수식 (a + 10)은 & 참조가 안되므로 우측값
```

# 값 카테고리

C++에서는 표현식의 값들에 대해서 식별자가 있는지, 이동이 지원되는지에 따라, 값을 다음처럼 분류했습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f8e1a357-ab67-4186-8cad-c5fdcda930ef)

|항목|내용|
|--|--|
|`lvalue`(left value)|좌측값으로서 변수와 같이 식별자가 있는 항목입니다.(`a = b;`와 같이 좌측값은 우측에 올 수도 있습니다.) 다른곳에서 참조할 수도 있기에 이동 연산이 지원되지 않습니다.(변수, 데이터 멤버의 이름, 함수, `&`포인터 연산, 전위 증감 연산, 문자열 상수등)<br/>대입문의 좌측에 올 수 있고, `&`로 주소를 얻어올 수 있으며, 표현식이 끝나더라도 존재합니다.|
|`xvalue`(expiring value)|`lvalue`나 `prvalue`를 `move()`할때 순간적으로 관리되는 값으로서 만료되어 가는 값입니다. 해당 주소로 접근했을때 값이 있을 수도 있고, 없을 수도 있습니다. 컴파일러만 사용하므로 `&`로 주소를 얻을 수 없으며, 표현식이 끝나면 소멸됩니다.|
|`prvalue`(pure rvalue)|순수한 rvalue로서 식별자가 없는 임시 개체나 수식들입니다.어짜피 임시로 생성된 것이므로 맘편히 삭제해도 되는 것들이며, 이동 연산이 지원됩니다.(수식, 후위 증감 연산, 문자열 상수를 제외한 상수)<br/>대입문의 우측에 올 수 있고, `&`로 주소를 얻어올 수 없으며, 표현식이 끝나면 소멸됩니다.|
|`glvalue`(generalized lvalue)|`xvalue`와 `lvalue`를 통칭합니다.| 
|`rvalue`(right value)|`xvalue`와 `prvalue`를 통칭합니다.|  

# 이동 연산(move semantics)

일반적으로 복사/대입보다는 이동이 성능이 좋을 수 있습니다.

다음은 일반적인 복사 생성자와 대입 연산자의 구현입니다. 복사 생성자에서는 새로운 메모리 공간을 할당해서 복사하고, 대입 연산자에서는 임시 개체를 생성하고, 바꿔치기 합니다.([포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)과 [swap을 이용한 예외 안전 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%84-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고)

```cpp
class Big {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {
        std::cout<<"Big : Default Constructor"<<std::endl; 
    }

    // 소멸자
    ~Big() {
        std::cout<<"Big : Destructor"<<std::endl; 
        delete[] m_Ptr;
    }

    // 복사 생성자
    Big(const Big& other) :
        m_Size(other.m_Size) {
        std::cout<<"Big : Copy Constructor"<<std::endl; 

        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 대입 연산자
    Big& operator =(const Big& other) {
        std::cout<<"Big : operator ="<<std::endl; 

        Big temp(other); // 복사 생성
        std::swap(m_Size, temp.m_Size); // 바꿔치기
        std::swap(m_Ptr, temp.m_Ptr);
        return *this;
    }

    size_t GetSize() const {return m_Size;}
}; 
Big a(10);
Big b(20);
a = b;
EXPECT_TRUE(a.GetSize() == 20);
EXPECT_TRUE(b.GetSize() == 20); 
```

실행 결과는 다음과 같습니다.

```cpp
Big : Default Constructor // a 생성
Big : Default Constructor // b 생성
Big : operator = 
Big : Copy Constructor // b를 temp에 복사
Big : Destructor // temp 소멸
Big : Destructor // b 소멸
Big : Destructor // a 소멸
```

여기서 만약 `b`가 `a`에 값을 대입하고, 더이상 사용되지 않는 임시 개체라면 어떨까요? 굳이 `temp`를 생성할게 아니라 `b`의 값을 `a`에 전달(이동)하면 됩니다.

다음 코드에서 `Move()`함수는 기존 것은 삭제하고, `other`것을 참조한뒤, 소유권 분쟁이 없도록 `other`는 초기화 했습니다.

```cpp
class Big {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {
        std::cout<<"Big : Default Constructor"<<std::endl; 
    }

    // 소멸자
    ~Big() {
        std::cout<<"Big : Destructor"<<std::endl; 
        delete[] m_Ptr;
    }

    // 복사 생성자
    Big(const Big& other) :
        m_Size(other.m_Size) {
        std::cout<<"Big : Copy Constructor"<<std::endl; 

        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 대입 연산자
    Big& operator =(const Big& other) {
        std::cout<<"Big : operator ="<<std::endl; 

        Big temp(other);
        std::swap(m_Size, temp.m_Size);
        std::swap(m_Ptr, temp.m_Ptr);
        return *this;
    }

    Big& Move(Big& other) {
        std::cout<<"Big : Move()"<<std::endl; 

        delete[] m_Ptr; // 기존 것은 삭제하고,
        m_Size = other.m_Size; // other 것을 저장한뒤
        m_Ptr = other.m_Ptr;

        other.m_Size = 0; // other는 초기화 합니다.
        other.m_Ptr = NULL;
        return *this;
    }
    
    size_t GetSize() const {return m_Size;}
}; 
Big a(10);
Big b(20);
a.Move(b);
EXPECT_TRUE(a.GetSize() == 20);   
EXPECT_TRUE(b.GetSize() == 0);   
```

실행 결과는 다음과 같습니다.

```cpp
Big : Default Constructor // a 생성
Big : Default Constructor // b 생성
Big : Move()
Big : Destructor // b 소멸
Big : Destructor // a 소멸
```

쓸데없이 `temp`가 생성되는게 빠졌습니다. 생성 부하나 복사 부하가 상당히 큰 개체라면 성능 향상을 기대할 수 있습니다.

# 우측값 참조, 이동 생성자, 이동 대입 연산자

C++ 표준 위원회는 여기서 더 나아가 그냥 각자 `Move()`함수를 구현하는 것에 만족하지 않고, 기존 `=`에서 식별자가 부여된(이름 있는) 왼쪽값(`lvalue`) 대입과 식별자가 부여되지 않은(이름 없는) 우측값(`rvalue`) 대입을 오버로딩해서 제공하고, `=`가 대입 연산자로 사용되거나 이동 대입 연산자로 사용되게 하여 기존 코드와 호환되면서 속도를 향상시킬 수 있도록 했습니다.

1. `=` 후 버려지는 이름 없는 임시 개체인 우측값(`rvalue`)를 사용하고, 
2. 이동 대입 연산자가 정의되어 있다면,

`=`시 이동을 수행하고, 그렇지 않으면 대입을 수행합니다.

이에 따라 우측값 참조 타입인 `&&` 와 이동 대입 연산자인 `T& operator =(T&& other)`가 제공됩니다.

다음 코드에서 `Move()`함수를 우측값을 참조하는 이동 대입 연산자로 변경하였으며, 테스트 코드에서 좌측값을 우측값으로 `static_cast`로 형변환(혹은 `std::move()`)하여 호출하였습니다. 

```cpp
class Big {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {}

    // 소멸자
    ~Big() {delete[] m_Ptr;}

    // 복사 생성자
    Big(const Big& other) :
        m_Size(other.m_Size) {
        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 대입 연산자. 좌측값(lvalue) 대입
    Big& operator =(const Big& other) {
        Big temp(other);
        std::swap(m_Size, temp.m_Size);
        std::swap(m_Ptr, temp.m_Ptr);
        return *this;
    }
    // 이동 대입 연산자. 우측값(rvalue) 이동
    Big& operator =(Big&& other) {
        delete[] m_Ptr; // 기존 것은 삭제하고,
        m_Size = other.m_Size; // other 것을 저장한뒤
        m_Ptr = other.m_Ptr;

        other.m_Size = 0; // other는 초기화 합니다.
        other.m_Ptr = NULL;
        return *this;
    }
    
    size_t GetSize() const {return m_Size;}
}; 

Big a(10);
Big b(20);

a = b; // b는 이름이 있는 lvalue. 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 20);   
EXPECT_TRUE(b.GetSize() == 20);  

a = static_cast<Big&&>(b); // b 를 이름이 없는 rvalue로 변환. 이동 대입 연산자 호출

EXPECT_TRUE(a.GetSize() == 20);   
EXPECT_TRUE(b.GetSize() == 0); // b는 이동되어 더이상 쓸 수 없음  

a = Big(30); // Big(30) 이름이 없는 rvalue를 생성. 이동 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 30);

Big c(40);
a = std::move(c); // c 를 이름이 없는 rvalue로 변환. static_cast<Big&&>(b)와 동일. 이동 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 40);
```

또한, 같은 방법으로 이동 생성자를 만들 수 있습니다.

```cpp
Big(Big&& other) : 
    m_Size(other.m_Size),
    m_Ptr(other.m_Ptr) {

    other.m_Size = 0; // other는 초기화 합니다.
    other.m_Ptr = NULL;
}

Big d(50);
Big e(std::move(d));
EXPECT_TRUE(d.GetSize() == 0); // d는 이동되어 더이상 쓸 수 없음  
EXPECT_TRUE(e.GetSize() == 50); 
```

기존 코드들에 `=`가 많이 사용되고 있을 텐데요, 이동 대입 연산자만 추가해 두면, 이름이 없는 임시 개체(우측값)가 대입될때, 대입 대신 이동을 하게 되어 속도가 향상될 수 있습니다. 

또한 명시적으로 작성한 좌측값 대입이라 할지라도, 약간의 리팩토링으로 성능을 향상시킬 수 있습니다.

전통적인 `Swap()`은 다음과 같은데요,

```cpp
void Swap(T& left, T& right) {
    T temp(right); // left가 덮어쓰기 전에 temp에 임시 저장
    right = left;
    left = temp;
}
```

이동 연산을 지원하면, 하기와 같이 리팩토링 하여 개선할 수 있습니다.

```cpp
void Swap(T& left, T& right) {
    T temp(std::move(right)); // right 값을 temp에 옮겨두고,
    right = std::move(left); // left를 right에 옮기고,
    left = std::move(temp); // temp를 left에 옮김
}
```

만약 이동 생성자나 이동 대입 연산자가 없다면, 복사 생성자나 대입 연산자를 호출합니다.

# 암시적 이동 생성자

이동 생성자는 다음과 같이 정의됩니다.

```cpp
T(T&& other);
```

복사 생성자가 암시적으로 생성되듯이 이동 생성자도 암시적으로 생성되며, 멤버별 이동 생성자를 호출합니다. 

단, 다음 조건에서는 암시적 이동 생성자를 생성하지 않습니다.

* 복사 생성자, 대입 연산자, 이동 대입 연산자, 소멸자를 사용자 정의한 경우
* 멤버 변수가 이동될 수 없는 경우
* 부모 클래스가 이동될 수 없는 경우

# 암시적 이동 대입 연산자

이동 대입 연산자는 다음과 같이 정의됩니다.

```cpp
T& operator =(T&& other);
```

대입 연산자가 암시적으로 생성되듯이 이동 대입 연산자도 암시적으로 생성되며, 멤버별 이동 대입 연산을 호출합니다.

단, 다음 조건에서는 암시적 이동 대입 연산자를 생성하지 않습니다.

* 복사 생성자, 이동 생성자, 대입 연산자, 소멸자를 사용자 정의한 경우
* 멤버 변수가 이동될 수 없는 경우
* 부모 클래스가 이동될 수 없는 경우
* `const` 타입의 멤버 변수가 있는 경우
* 참조 타입의 멤버 변수가 있는 경우

# move()

개체의 타입이 `T` 라고 할때

1. `T&`는 개체의 왼쪽값 참조라 하고,
2. `T&&`는 개체의 우측값 참조라고 합니다.

`move()`는 왼쪽값(`lvalue`) 을 우측값(`rvalue`)으로 형변환 합니다. 

다음처럼 `static_cast`로도 형변환 할 수 있으나, 가독성을 위해 `move()`를 사용합니다.

```cpp
T t;
static_cast<T&&>(t); // (△) 비권장. 가독성이 떨어집니다.
std::move(t); // (O) 이동 연산을 위해 rvalue로 변환합니다.
```

# forward()

값 카테고리 유지하며 인자를 다른 함수에 전달합니다.

우측값(`rvalue`)을 `T&& val`와 같이 함수의 인자로 전달받는 순간, 이름을 갖게 되고, 왼쪽값(`lvalue`)이 되버립니다. 이름이 있으니, 그 이름을 가지고 다른데 사용할 수 있어서 더이상 임시 개체가 아닌 거죠. 

다음 예에서

1. `g(std::move(t))`와 같이 우측값 참조를 사용하여 `g(T&& val)`가 호출되지만,
2.  `f(val) + 2`에서 `val`은 왼쪽값이기 때문에 `f(T& val)`을 호출합니다.

```cpp
class T {};

int f(T& val) {return 10;}
int f(T&& val) {return 20;}

int g(T& val) {return f(val) + 1;}
int g(T&& val) {return f(val) + 2;} // 여기서 val은 이름이 있으니 우측값이 아닙니다. 그래서 f(T& val)을 호출합니다.

T t;

// g(T& val)와 f(T& val)가 호출되어 11입니다.
EXPECT_TRUE(g(t) == 11); 

// g(T&& val)와 f(T& val)가 호출되어 12입니다.
EXPECT_TRUE(g(std::move(t)) == 12);
```

우측값 참조(`&&`)가 좌측값 참조(`&`) 로 변경되어 버렸습니다. 이해는 됩니다. `val`로 명명 되는 순간 더이상 임시 개체가 아니니까요. 이를 해결하려면 다시 한번 `move()`를 사용하면 되는데요, 값 카테고리를 유지한다는 의미로 내부적으로 `move()`와 동일한 `forward()`를 사용합니다.

```cpp
class T {};

int f(T& val) {return 10;}
int f(T&& val) {return 20;}

int g(T& val) {return f(val) + 1;}
// int g(T&& val) {return f(std::move(val)) + 2;} 
int g(T&& val) {return f(std::forward<T>(val)) + 2;} // 내부적으론 move와 동일하나 값 카테고리를 유지한채 전달한다는 의미로 forward 사용

T t;

// g(T& val)와 f(T& val)가 호출되어 11입니다.
EXPECT_TRUE(g(t) == 11); 

// g(T&& val)와 f(T&& val)가 호출되어 22입니다.
EXPECT_TRUE(g(std::move(t)) == 22);
```


