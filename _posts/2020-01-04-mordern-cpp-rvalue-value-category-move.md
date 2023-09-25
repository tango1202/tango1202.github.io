---
layout: single
title: "#4. [모던 C++] (C++11~) 우측값, 이동 연산, 이동 생성자, 이동 대입 연산자, move(), forward(), 값 카테고리"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 이동 연산을 위해 우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자가 추가되어 임시 개체 대입시 속도가 향상되었습니다.

# 개요

C++의 STL을 보면 대부분 값 기반 복사/대입([컨테이너 요소 규칙](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%9A%94%EC%86%8C-%EA%B7%9C%EC%B9%99) 참고)이 이루어져 속도 성능이 저하될 수 있습니다. 이런 문제를 해결하고자 C++11 부터 이동 연산을 지원하게 되었으며, 우측값과 값 카테고리에 대한 이해가 있어야 이를 활용할 수 있습니다. 포인터나 참조자도 어려웠습니다만, 우측값과 값 카테고리는 더욱 더 어렵습니다. 하지만 이를 이해하지 않고는 모던 C++를 활용할 수 없기에 꼭 이해해야만 합니다.

우측값과 이동 연산의 컨셉은 다음과 같습니다. 다음 코드를 예로 들면,

1. 오른쪽 표현식(`a + b`)의 결과 값은 임시 변수임.
2. 임시 변수는 어짜피 버려질 것이므로 굳이 `c`에 복사하지 말고 이동시키면 속도가 조금이라도 빨라짐.

```cpp
T a, b, c; // a, b, c 는 좌측값
c = a + b; // a + b 연산 결과는 임시 변수로서 우측값
```

# 좌측값(lvalue, left Value)과 우측값(rvalue, right value)

C에서의 정의는 다음과 같습니다.

|항목|내용|
|--|--|
|좌측값|변수처럼 대입시에 왼쪽 또는 오른쪽에 올 수 있는 표현식|
|우측값|일련의 수식처럼 대입시에 왼쪽에만 올 수 있는 표현식|

C++의 정의는 좀더 구체적으로 다음과 같습니다.

|항목|내용|
|--|--|
|좌측값|변수처럼 어떠한 메모리 위치를 가리키며 `&`로 그 위치를 참조할 수 있는 표현식|
|우측값|좌측값이 아닌 값. 즉 위치를 참조할 수 없는 임시 변수나 상수값(문자열 상수는 주소가 있으므로 좌측값)|

즉, 다음처럼 `&`연산이 불가능하면 우측값입니다.

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
|`xvalue`(expiring value)|`lvalue`나 `prvalue`를 `std::move()`할때 순간적으로 관리되는 값으로서 만료되어 가는 값입니다. 해당 주소로 접근했을때 값이 있을 수도 있고, 없을 수도 있습니다. 컴파일러만 사용하므로 `&`로 주소를 얻을 수 없으며, 표현식이 끝나면 소멸됩니다.|
|`prvalue`(pure rvalue)|순수한 rvalue로서 식별자가 없는 임시 개체나 수식들입니다. 어짜피 임시로 생성된 것이므로 맘편히 삭제해도 되는 것들이며, 이동 연산이 지원됩니다.(수식, 후위 증감 연산, 문자열 상수를 제외한 상수, 람다 표현식)<br/>대입문의 우측에 올 수 있고, `&`로 주소를 얻어올 수 없으며, 표현식이 끝나면 소멸됩니다.|
|`glvalue`(generalized lvalue)|`xvalue`와 `lvalue`를 통칭합니다.| 
|`rvalue`(right value)|`xvalue`와 `prvalue`를 통칭합니다.|  

# 이동 연산(move semantics)

일반적으로 복사/대입보다는 이동이 성능이 좋을 수 있습니다.

다음은 일반적인 복사 생성자와 대입 연산자의 구현입니다. 복사 생성자에서는 새로운 메모리 공간을 할당해서 복사하고, 대입 연산자에서는 임시 개체를 생성한뒤 `swap()`으로 바꿔치기 합니다.([포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)과 [swap을 이용한 예외 안전 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%84-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고)

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

여기서 만약 `b`가 `a`에 값을 대입하고, 더이상 사용되지 않는 임시 개체(우측값)라면 어떨까요? 굳이 `temp`를 생성해서 복제본을 만들 필요없이 `b`의 값을 바로 `a`에 전달(이동)해도 됩니다. `a`가 기존 것을 버리고, `b` 데이터만 가지면 되니까요. 

다음 그림에서 `a = b;`등으로 `a`가 `b`의 데이터를 갖도록 만들때, 복사 대입과 이동 대입의 차이를 도식화 했습니다. 이동 대입이 훨씬 간결한 것을 알 수 있습니다.(다만, `b`가 참조하는 데이터는 없어집니다.)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e4333269-db1f-41e5-8ec7-308db82869a2)


다음 코드에서 `Move()`함수는 기존 것은 삭제하고, `other`것을 참조한 뒤, 소유권 분쟁이 없도록 `other`는 초기화 했습니다.

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

    // 이동 함수
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

C++ 표준 위원회는 여기서 더 나아가 그냥 각자 `Move()`함수를 구현하는 것에 만족하지 않고, 기존 `=`에서 식별자가 부여된(이름 있는) 좌측값(`lvalue`) 대입과 식별자가 부여되지 않은(이름 없는) 우측값(`rvalue`) 대입을 오버로딩해서 제공하고, `=`가 대입 연산자로 사용되거나 이동 대입 연산자로 사용되게 하여 기존 코드와 호환되면서 속도를 향상시킬 수 있도록 했습니다.

1. `=` 의 인수가 우측값(`rvalue`) 이고(대입 후 버려지는 이름 없는 임시 개체), 
2. 이동 대입 연산자가 정의되어 있다면,

`=`시 이동을 수행하고, 그렇지 않으면 대입을 수행합니다.

이에 따라 C++11 부터는 우측값 참조 타입인 `&&` 와 이동 대입 연산자인 `T& operator =(T&& other)`가 제공됩니다.

다음 코드에서 `Move()`함수를 우측값을 참조하는 이동 대입 연산자로 변경하였으며, 테스트 코드에서 우측값을 사용하기 위해

1. `static_cast`을 이용하여 좌측값을 우측값으로 형변환
2. `std::move()`를 이용하여 좌측값을 우측값으로 형변환
3. `Big(40)`로 임시 개체를 생성(임시 개체는 우측값입니다.)하여 호출하였습니다. 
 
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

Big c(30);
a = std::move(c); // c 를 이름이 없는 rvalue로 변환. static_cast<Big&&>(c)와 동일. 이동 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 30);

a = Big(40); // Big(40) 이름이 없는 rvalue를 생성. 이동 대입 연산자 호출
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

# 이동 연산을 이용한 리팩토링

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

이동 연산을 지원하면, 하기와 같이 리팩토링 하여 개선할 수 있습니다. 만약 이동 생성자나 이동 대입 연산자가 없다면, 복사 생성자나 대입 연산자를 호출하여 기존 코드와 동일하게 작동됩니다.

```cpp
void Swap(T& left, T& right) {
    T temp(std::move(right)); // right 값을 temp에 옮겨두고,
    right = std::move(left); // left를 right에 옮기고,
    left = std::move(temp); // temp를 left에 옮김
}
```

# 암시적 이동 생성자

이동 생성자는 다음과 같이 정의됩니다.

```cpp
T(T&& other);
```

복사 생성자가 암시적으로 생성되듯이([암시적 복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90) 참고) 이동 생성자도 암시적으로 생성되며, 멤버별 이동 생성자를 호출합니다. 

단, 다음 조건에서는 암시적 이동 생성자를 생성하지 않습니다.

* 복사 생성자, 대입 연산자, 이동 대입 연산자, 소멸자를 사용자 정의한 경우
* 멤버 변수가 이동될 수 없는 경우
* 부모 클래스가 이동될 수 없는 경우

# 암시적 이동 대입 연산자

이동 대입 연산자는 다음과 같이 정의됩니다.

```cpp
T& operator =(T&& other);
```

대입 연산자가 암시적으로 생성되듯이([암시적 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고) 이동 대입 연산자도 암시적으로 생성되며, 멤버별 이동 대입 연산을 호출합니다.

단, 다음 조건에서는 암시적 이동 대입 연산자를 생성하지 않습니다.

* 복사 생성자, 이동 생성자, 대입 연산자, 소멸자를 사용자 정의한 경우
* 멤버 변수가 이동될 수 없는 경우
* 부모 클래스가 이동될 수 없는 경우
* `const` 타입의 멤버 변수가 있는 경우
* 참조 타입의 멤버 변수가 있는 경우

# move()

개체의 타입이 `T` 라고 할때

1. `T&`는 개체의 좌측값 참조라 하고,
2. `T&&`는 개체의 우측값 참조라고 합니다.

`std::move()`는 좌측값(`lvalue`) 을 우측값(`rvalue`)으로 형변환 합니다. 

다음처럼 `static_cast`로도 형변환 할 수 있으나, 가독성을 위해 `std::move()`를 사용합니다.

```cpp
T t;
static_cast<T&&>(t); // (△) 비권장. 가독성이 떨어집니다.
std::move(t); // (O) 이동 연산을 위해 rvalue로 변환합니다.
```

다음은 `Func()`을 `lvalue`와 `rvalue`를 오버로드 한 예입니다. `lvalue`인지 `rvalue`인지에 따라 다른 버전이 호출 됩니다.

```cpp
class A{};
class T {
public:
    static int Func(A&) {return 1;} // #1. lvalue
    static int Func(A&&) {return 2;} // #2. rvalue
};

A a;

EXPECT_TRUE(T::Func(a) == 1); // 이름이 있는 lvalue
EXPECT_TRUE(T::Func(static_cast<A&&>(a)) == 2); // 강제 형변환 해서 rvalue
EXPECT_TRUE(T::Func(std::move(a)) == 2); // lvalue를 move 해서 rvalue

A& b = a;
EXPECT_TRUE(T::Func(b) == 1);  
EXPECT_TRUE(T::Func(static_cast<A&&>(b)) == 2);
EXPECT_TRUE(T::Func(std::move(b)) == 2);

EXPECT_TRUE(T::Func(A()) == 2); // 임시 개체는 rvalue
EXPECT_TRUE(T::Func(std::move(A())) == 2); // 임시 개체를 move 해도 rvalue
```

# move_if_noexcept()

`std::move_if_noexcept()` 는 nothrow 보증이 되는 경우에만 `&&`로 형변환 합니다. 

다음 코드를 보면 `A`는 예외 보증이 안되어 있고, `B`는 `noexcept`가 지정되어 있는데요,

1. `A`의 경우는 `noexcept`가 없어 복사 생성자가 호출되고,
2. `B`의 경우는 `noexcept`가 있어 이동 생성자가 호출됩니다.

```cpp
class A {
public:
    A() {}
    // noexcept가 없어서 예외가 발생할 수도 있습니다.
    A(const A&) {std::cout<<"A : Copy Constructor"<<std::endl;}
    A(A&&) {std::cout<<"A : Move Constructor"<<std::endl;;} 
};
class B {
public:
    B() {}
    // nothrow 보증합니다.
    B(const B&) noexcept {std::cout<<"B : Copy Constructor"<<std::endl;;}
    B(B&&) noexcept {std::cout<<"B : Move Constructor"<<std::endl;;} 
};

A a1;
A a2 = std::move_if_noexcept(a1); // A(A&&)가 nothrow 예외 보증이 되지 않아 그냥 A(const A&)를 호출합니다.

B b1;
B b2 = std::move_if_noexcept(b1); // B(B&&)가 nothrow 예외 보증이 되어 B(B&&)를 호출합니다.
```

# forward()

인자의 값 카테고리를 유지하며 다른 함수에 전달합니다.

우측값(`rvalue`)을 `T&& val`와 같이 함수의 인자로 전달받는 순간, 이름을 갖게 되어 좌측값(`lvalue`)이 되버립니다. 이름이 있으니, 그 이름을 가지고 다른데 사용할 수 있어서 더이상 임시 개체가 아닌 거죠. 

다음 예에서

1. `g(std::move(t))`와 같이 우측값 참조를 사용하여 `g(T&& val)`가 호출되지만,
2.  `f(val) + 2`에서 `val`은 좌측값이기 때문에 `f(T& val)`을 호출합니다.

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

우측값 참조(`&&`)가 좌측값 참조(`&`) 로 변경되어 버렸습니다. 이해는 됩니다. `val`로 명명 되는 순간 더이상 임시 개체가 아니니까요. 이를 해결하려면 다시 한번 `std::move()`를 사용하여 우측값으로 바꿀 수도 있으나, 값 카테고리를 유지하여 전달한다은 의미로 `std::forward()`를 사용하는게 좋습니다.

```cpp
class T {};

int f(T& val) {return 10;}
int f(T&& val) {return 20;}

int g(T& val) {return f(val) + 1;}
// int g(T&& val) {return f(std::move(val)) + 2;} // (△) 비권장. 이동 연산을 수행한다는 느낌이어서 가독성이 떨어집니다.
int g(T&& val) {return f(std::forward<T>(val)) + 2;} // 값 카테고리를 유지한채 전달한다는 의미로 forward 사용

T t;

// g(T& val)와 f(T& val)가 호출되어 11입니다.
EXPECT_TRUE(g(t) == 11); 

// g(T&& val)와 f(T&& val)가 호출되어 22입니다.
EXPECT_TRUE(g(std::move(t)) == 22);
```

