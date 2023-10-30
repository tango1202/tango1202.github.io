---
layout: single
title: "#7. [모던 C++] (C++11~) 우측값, 이동 연산, 이동 생성자, 이동 대입 연산자, move(), forward(), 값 카테고리"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * [MEC++#17] 특수 멤버 함수들의 자동 작성 조건을 숙지하라.([이동 연산에 따른 암시적 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%97%90-%EB%94%B0%EB%A5%B8-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98))
> * [MEC++#23] std::move와 std::forward를 숙지하라.

> * [MEC++#24] 전달 참조와 우측값 참조를 구별하라.([전달 참조](??))


> * (C++11~) [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)을 위해 [우측값 참조(`&&`)와 이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가되어 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4) 대입시 속도가 향상되었습니다.
> * (C++11~) [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90), [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 추가됨에 따라 [기본 생성자, 복사 생성자, 복사 대입 연산자, 이동 생성자, 이동 대입 연산자, 소멸자의 암시적 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%97%90-%EB%94%B0%EB%A5%B8-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98)가 재정립 되었습니다.
> * (C++14~) [exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)는 주어진 값을 바꾸고 이전값을 리턴합니다. [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.

# 개요

C++의 STL을 보면 대부분 값 기반 복사/대입([컨테이너 요소 규칙](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-container/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%9A%94%EC%86%8C-%EA%B7%9C%EC%B9%99) 참고)이 이루어져 속도 성능이 저하될 수 있습니다. 이런 문제를 해결하고자 C++11 부터 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)을 지원하게 되었으며, 우측값과 값 카테고리에 대한 이해가 있어야 이를 활용할 수 있습니다. [포인터나 참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/)도 어려웠습니다만, 우측값과 값 카테고리는 더욱 더 어렵습니다. 하지만 이를 이해하지 않고는 모던 C++를 활용할 수 없기에 꼭 이해해야만 합니다.

우측값과 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)의 컨셉은 다음과 같습니다. 다음 코드를 예로 들면,

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
|우측값|좌측값이 아닌 값. 즉 위치를 참조할 수 없는 임시 변수나 상수값(*[문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)는 주소가 있으므로 좌측값*)|

즉, 다음처럼 `&`연산으로 ***포인터 주소값을 얻을 수 없으면 우측값***입니다.

```cpp
int a = 0; 
int* ptr1 = &a; // a는 &로 위치 참조가 되므로 좌측값
int* ptr2 = &0; // (X) 컴파일 오류. 상수 0은 & 위치 참조가 안되므로 우측값
int b = &(a + 10); // (X) 컴파일 오류. 수식 (a + 10)은 & 위치 참조가 안되므로 우측값
```

혹은 [참조자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/#%EC%95%88%EC%A0%95%EC%A0%81%EC%9D%B8-%EC%B0%B8%EC%A1%B0%EC%9E%90)로 저장할 수 없으면 우측값입니다.

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
|`lvalue`(left value)|좌측값으로서 변수와 같이 식별자가 있는 항목입니다.(`a = b;`와 같이 좌측값은 우측에 올 수도 있습니다.) 다른곳에서 참조할 수도 있기에 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)이 지원되지 않습니다.(변수, 데이터 멤버의 이름, 함수, `&`포인터 연산, 전위 증감 연산, [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)등)<br/>대입문의 좌측에 올 수 있고, `&`로 주소를 얻어올 수 있으며, 표현식이 끝나더라도 존재합니다.|
|`xvalue`(eXpiring value)|`lvalue`나 `prvalue`를 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)할때 순간적으로 관리되는 값으로서 만료되어 가는 값입니다. 해당 주소로 접근했을때 값이 있을 수도 있고, 없을 수도 있습니다. 컴파일러만 사용하므로 `&`로 주소를 얻을 수 없으며, 표현식이 끝나면 소멸됩니다.|
|`prvalue`(pure rvalue)|순수한 `rvalue`로서 식별자가 없는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)나 수식들입니다. 어짜피 임시로 생성된 것이므로 맘편히 삭제해도 되는 것들이며, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)이 지원됩니다.(수식, 후위 증감 연산, [문자열 상수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-literals/#%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%83%81%EC%88%98)를 제외한 상수, 람다 표현식)<br/>대입문의 우측에 올 수 있고, `&`로 주소를 얻어올 수 없으며, 표현식이 끝나면 소멸됩니다.|
|`glvalue`(generalized lvalue)|`xvalue`와 `lvalue`를 통칭합니다.| 
|`rvalue`(right value)|`xvalue`와 `prvalue`를 통칭합니다.|  

# 이동 연산(move semantics)

일반적으로 복사/대입보다는 이동이 성능이 좋을 수 있습니다.

다음은 일반적인 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 구현입니다. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)에서는 새로운 메모리 공간을 할당해서 복사하고, [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)에서는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 생성한뒤 `swap()`으로 바꿔치기 합니다.([포인터 멤버 변수의 소유권 분쟁](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EC%9D%98-%EC%86%8C%EC%9C%A0%EA%B6%8C-%EB%B6%84%EC%9F%81)과 [swap을 이용한 예외 안전 복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#swap%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8-%EC%95%88%EC%A0%84-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 참고)

```cpp
class Big {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {
        std::cout << "Big : Default Constructor" << std::endl; 
    }

    // 소멸자
    ~Big() {
        std::cout << "Big : Destructor" << std::endl; 
        delete[] m_Ptr;
    }

    // 복사 생성자
    Big(const Big& other) :
        m_Size(other.m_Size) {
        std::cout << "Big : Copy Constructor" << std::endl; 

        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 복사 대입 연산자
    Big& operator =(const Big& other) {
        std::cout << "Big : operator =" << std::endl; 

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

여기서 만약 `b`가 `a`에 값을 대입하고, 더이상 사용되지 않는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(우측값)라면 어떨까요? 굳이 `temp`를 생성해서 복제본을 만들 필요없이 `b`의 값을 바로 `a`에 전달(이동)해도 됩니다. `a`가 기존 것을 버리고, `b` 데이터만 가지면 되니까요. 

다음 그림에서 `a = b;`등으로 `a`가 `b`의 데이터를 갖도록 만들때, 복사 대입과 이동 대입의 차이를 도식화 했습니다. 이동 대입이 훨씬 간결한 것을 알 수 있습니다.(*다만, `b`가 참조하는 데이터는 없어집니다.*)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/e4333269-db1f-41e5-8ec7-308db82869a2)

다음 코드에서 `Move()`함수는 데이터를 이동시킵니다. 기존 것은 삭제하고, `other`것을 참조한 뒤, 소유권 분쟁이 없도록 `other`는 초기화 합니다.

```cpp
class Big {
    size_t m_Size;
    char* m_Ptr; // 크기가 큰 데이터

public:    
    explicit Big(size_t size) : 
        m_Size(size), 
        m_Ptr(new char[size]) {
        std::cout << "Big : Default Constructor" << std::endl; 
    }

    // 소멸자
    ~Big() {
        std::cout << "Big : Destructor" << std::endl; 
        delete[] m_Ptr;
    }

    // 복사 생성자
    Big(const Big& other) :
        m_Size(other.m_Size) {
        std::cout << "Big : Copy Constructor" << std::endl; 

        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 복사 대입 연산자
    Big& operator =(const Big& other) {
        std::cout << "Big : operator =" << std::endl; 

        Big temp(other);
        std::swap(m_Size, temp.m_Size);
        std::swap(m_Ptr, temp.m_Ptr);
        return *this;
    }

    // 이동 함수
    Big& Move(Big& other) {
        std::cout << "Big : Move()" << std::endl; 

        delete[] m_Ptr; // 기존 것은 삭제하고,
        m_Size = other.m_Size; // other 것을 저장한뒤
        m_Ptr = other.m_Ptr;

        other.m_Size = 0; // other는 초기화 합니다.
        other.m_Ptr = nullptr;
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

C++ 표준 위원회는 여기서 더 나아가 그냥 각자 `Move()`함수를 구현하는 것에 만족하지 않고, 기존 `=`에서 식별자가 부여된(*이름이 있는*) 좌측값(`lvalue`) 대입과 식별자가 부여되지 않은(*이름이 없는*) 우측값(`rvalue`) 대입을 오버로딩해서 제공하고, `=`가 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)로 사용되거나 이동 대입 연산자로 사용되게 하여 기존 코드와 호환되면서 속도를 향상시킬 수 있도록 했습니다.

1. `=` 의 인수가 우측값(`rvalue`) 이고(*대입 후 버려지는 이름 없는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)*), 
2. 이동 대입 연산자가 정의되어 있다면,

`=`시 이동 대입을 수행하고, 그렇지 않으면 복사 대입을 수행합니다.

이에 따라 C++11 부터는 

* 우측값 참조 타입인 `&&`와 
* 이동 생성자인 `T(T&& other)`와
* 이동 대입 연산자인 `T& operator =(T&& other)`가 제공됩니다.

다음 코드에서 `Move()`함수를 우측값을 참조하는 이동 대입 연산자로 변경하였으며, 테스트 코드에서 우측값을 사용하기 위해

1. [static_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 이용하여 좌측값을 우측값으로 형변환
2. [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 이용하여 좌측값을 우측값으로 형변환
3. `Big_11(40)`로 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)를 생성(*[임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)는 우측값입니다.*)하여 호출하였습니다. 
 
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
        // 메모리 공간 할당
        m_Ptr = new char[m_Size];

        // 메모리 복사
        memcpy(m_Ptr, other.m_Ptr, m_Size);
    }

    // 복사 대입 연산자. 좌측값(lvalue) 대입
    Big_11& operator =(const Big_11& other) {
        Big_11 temp(other);
        std::swap(m_Size, temp.m_Size);
        std::swap(m_Ptr, temp.m_Ptr);
        return *this;
    }
    // 이동 대입 연산자. 우측값(rvalue) 이동
    Big_11& operator =(Big_11&& other) {
        delete[] m_Ptr; // 기존 것은 삭제하고,
        m_Size = other.m_Size; // other 것을 저장한뒤
        m_Ptr = other.m_Ptr;

        other.m_Size = 0; // other는 초기화 합니다.
        other.m_Ptr = nullptr;
        return *this;
    }
    
    size_t GetSize() const {return m_Size;}
}; 

Big_11 a(10);
Big_11 b(20);

a = b; // b는 이름이 있는 lvalue. 복사 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 20);   
EXPECT_TRUE(b.GetSize() == 20);  

a = static_cast<Big_11&&>(b); // b 를 이름이 없는 rvalue로 변환. 이동 대입 연산자 호출

EXPECT_TRUE(a.GetSize() == 20);   
EXPECT_TRUE(b.GetSize() == 0); // b는 이동되어 더이상 쓸 수 없음  

Big_11 c(30);
a = std::move(c); // c 를 이름이 없는 rvalue로 변환. static_cast<Big&&>(c)와 동일. 이동 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 30);

a = Big_11(40); // Big(40) 이름이 없는 rvalue를 생성. 이동 대입 연산자 호출
EXPECT_TRUE(a.GetSize() == 40);
```

또한, 같은 방법으로 이동 생성자를 만들 수 있습니다.

```cpp
// 이동 생성자
Big_11(Big_11&& other) : 
    m_Size(other.m_Size),
    m_Ptr(other.m_Ptr) {

    other.m_Size = 0; // other는 초기화 합니다.
    other.m_Ptr = nullptr;
}  

Big_11 d(50);
Big_11 e(std::move(d));
EXPECT_TRUE(d.GetSize() == 0); // d는 이동되어 더이상 쓸 수 없음  
EXPECT_TRUE(e.GetSize() == 50); 
```
> *(C++14~) [exchange()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-exchange/)는 주어진 값을 바꾸고 이전값을 리턴합니다. [이동 생성자와 이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9A%B0%EC%B8%A1%EA%B0%92-%EC%B0%B8%EC%A1%B0-%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90) 구현에 활용할 수 있습니다.*

# 이동 연산 변환의 안전성

C++11의 컴파일러는 기존 복사 연산을 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)으로 대체하여 성능을 향상시킵니다. 다만, 기존 코드와의 호환성이 보장될때만 대체합니다. 

한마디로, ***가능하면 이동하되, 필요하면 복사합니다.***

[vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)의 `push_back()`을 보면 새로운 요소를 추가할때 [용량](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/#size%EC%99%80-capacity)이 부족하다면, 새로운 메모리 영역을 할당하고, 기존 요소를 새로운 메모리 영역에 복사하고, 새로운 요소를 추가합니다. 이때 요소의 복사 과정에서 예외가 발생하더라도, 원본 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)는 그대로 이므로 `push_back()`은 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 된다고 할 수 있습니다.

하지만 이를 이동 연산으로 하면 어떨까요?

기존 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)의 요소들을 이동하는 중에 예외가 발생한다면, 원본 [vector](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-vector/)는 이미 수정된 상태이기에 [강한 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 된다고 할 수 없습니다. 따라서, 컴파일러가 마음 놓고 복사 연산을 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)으로 바꿀 수는 없습니다. 이전 코드의 예외 안전성을 해치니까요.

그래서, 컴파일러는 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)이 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 선언되어 예외를 방출하지 않는 경우에만 복사 연산을  [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)으로 대체한다고 합니다.

그러니  [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)을 위한 이동 생성자와 이동 대입 연산자는 최대한 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)로 만드는게 좋습니다. 그리고, [nothrow swap](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#nothrow-swap---%ED%8F%AC%EC%9D%B8%ED%84%B0-%EB%A9%A4%EB%B2%84-%EB%B3%80%EC%88%98%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-swap-%EC%B5%9C%EC%A0%81%ED%99%94) 도요.(*궁극적으로 모든 함수가 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)면 어떨까... 상상만 해봅니다.*)


# 이동 연산을 이용한 리팩토링

기존 코드들에 `=`가 많이 사용되고 있을 텐데요, 이동 대입 연산자만 추가해 두면, 이름이 없는 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)(*우측값*)가 대입될때, 복사 대입 대신 이동 대입을 하게 되어 속도가 향상될 수 있습니다. 

또한 명시적으로 작성한 좌측값 대입이라 할지라도, 약간의 리팩토링으로 성능을 향상시킬 수 있습니다.

전통적인 `Swap()`은 다음과 같은데요,

```cpp
void Swap(T& left, T& right) {
    T temp(right); // left가 덮어쓰기 전에 temp에 임시 저장
    right = left;
    left = temp;
}
```

다음과 같이 변경하면, 내부적으로 [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)을 수행하기 때문에 속도를 개선할 수 있습니다. 만약 이동 생성자나 이동 대입 연산자가 없다면, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 호출하여 기존 코드와 동일하게 작동되므로 코드 호환성도 보장됩니다.

```cpp
void Swap_11(T& left, T& right) {
    T temp(std::move(right)); // right 값을 temp에 옮겨두고,
    right = std::move(left); // left를 right에 옮기고,
    left = std::move(temp); // temp를 left에 옮김
}
```

# 이동 생성자

이동 생성자는 다음과 같이 정의됩니다.

```cpp
T(T&& other);
```

[복사 생성자가 암시적으로 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)되듯이 이동 생성자도 암시적으로 생성되며, 멤버별 이동 생성자를 호출합니다.(*단, 멤버별 이동 생성자가 정의되지 않았다면, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)를 호출합니다.*)

단, 다음 조건에서는 암시적 이동 생성자를 생성하지 않습니다.

* [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), 이동 대입 연산자, 소멸자를 사용자 정의한 경우
* 멤버 변수가 이동될 수 없는 경우
* 부모 클래스가 이동될 수 없는 경우

# 이동 대입 연산자

이동 대입 연산자는 다음과 같이 정의됩니다.

```cpp
T& operator =(T&& other);
```

[복사 대입 연산자가 암시적으로 생성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EC%95%94%EC%8B%9C%EC%A0%81-%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)되듯이 이동 대입 연산자도 암시적으로 생성되며, 멤버별 이동 대입 연산을 호출합니다.(*단, 멤버별 이동 대입 연산자가 정의되지 않았다면, [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 호출합니다.*)

단, 다음 조건에서는 암시적 이동 대입 연산자를 생성하지 않습니다.

* [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), 이동 생성자, [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), 소멸자를 사용자 정의한 경우
* 멤버 변수가 이동될 수 없는 경우
* 부모 클래스가 이동될 수 없는 경우
* [const](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/) 타입의 멤버 변수가 있는 경우
* 참조 타입의 멤버 변수가 있는 경우

# 이동 연산에 따른 암시적 정의

[클래스의 암시적 정의](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-implicit-definition/)에 언급한 것처럼, 기존에는 [기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), 소멸자만 암시적으로 정의했는데요, C++11 부터는 이동 생성자와 이동 대입 연산자의 암시적 정의도 추가되어 약간 변경되었습니다. 눈여겨 볼 점은 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)의 암시적 정의 규칙인데요, 이동 생성자나 이동 대입 연산자를 정의하면 암시적으로 정의되지 않습니다.

암시적으로 정의되는 조건은 다음과 같습니다.

|항목|내용|
|--|--|
|[기본 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EA%B8%B0%EB%B3%B8-%EC%83%9D%EC%84%B1%EC%9E%90)|기존과 동일하게 사용자 정의한 다른 생성자가 없으면 암시적으로 정의합니다.|
|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)|기존과 동일하게 사용자 정의한 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 없으면 암시적으로 정의됩니다. ***단, C++11에 추가된 이동 생성자나 이동 대입 연산자가 정의되면 암시적으로 정의되지 않습니다.***|
|[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)|기존과 동일하게 사용자 정의한 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)가 없으면 암시적으로 정의됩니다. ***단, C++11에 추가된 이동 생성자나 이동 대입 연산자가 정의되면 암시적으로 정의되지 않습니다.***|
|이동 생성자|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), 이동 생성자, 이동 대입 연산자, [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), 소멸자를 사용자 정의한 경우에는 정의되지 않습니다.(*세부적인 규칙은 [이동 생성자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%83%9D%EC%84%B1%EC%9E%90)를 참고하세요.*)|
|이동 대입 연산자|[복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), 이동 생성자, 이동 대입 연산자, [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), 소멸자를 사용자 정의한 경우에는 정의되지 않습니다.(*세부적인 규칙은 [이동 대입 연산자](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 참고하세요.*)|

# 암시적 이동 생성자와 암시적 이동 대입 연산자의 default 정의

암시적 이동 생성자와 암시적 이동 대입 연산자의 자동 생성 조건은 상당히 까다롭습니다. [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90), 이동 생성자, [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90), 이동 대입 연산자, 소멸자를 사용자 중 한개라도 정의되면 안되니까요.

이렇게 까다로운 이유는, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)/이동 생성자/[복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)/이동 대입 연산자/소멸자중 한개라도 정의되었다는 의미는 개체의 생성/대입/이동/소멸중 뭔가 사용자가 특별히 처리한 부분이 있다는 뜻이기 때문입니다.

암시적 버전을 어설프게 제공했다가 호환이 안될 수 있죠. 특히 [이동 연산을 이용한 리팩토링](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%A6%AC%ED%8C%A9%ED%86%A0%EB%A7%81)에서 언급했듯, [이동 연산](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/)은 이동 생성자나 이동 대입 연산자가 없다면, [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)나 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)를 호출하기 때문에 동작상의 문제는 없으니 아예 제공은 안해버린 거죠.

하지만 이 부분은 리팩토링 하다 보면 암시적 이동 생성자와 암시적 이동 대입 연산자가 의도치 않게 가려지는 사이드 이펙트를 유발합니다.

예를 들어 데이터를 관리하는 `CopyableMoveable` 개체가 있다고 합니다. `T`타입의 개체를 복사 생성하거나 이동 생성합니다.

```cpp
// 관리하는 개체를 복사 생성이나 이동 생성 합니다.
template<typename T>
class CopyableMoveable_11 {
    T* m_Data;
public:
    CopyableMoveable_11() : m_Data(nullptr) {}

    explicit CopyableMoveable_11(T* data) : m_Data(data) {}
    
    // 복사 생성
    CopyableMoveable_11(const CopyableMoveable_11& other) : 
        m_Data(other.m_Data != nullptr ? new T(*other.m_Data) : nullptr) {
        std::cout << "CopyableMoveable_11 : Copy Constructor" << std::endl;       
    } 

    // 이동 생성
    CopyableMoveable_11(CopyableMoveable_11&& other) : 
        m_Data(other.m_Data) {
        std::cout << "CopyableMoveable_11 : Move Constructor" << std::endl;   

        other.m_Data = nullptr; // other는 초기화 합니다.
    }  

    ~CopyableMoveable_11() {delete m_Data;}
};
```

다음과 같이 `A_11`에서 사용하면 별 문제 없어 보입니다.

```cpp
class A_11 {
private:
    CopyableMoveable_11<int> m_Data;
public:
    explicit A_11(int* data) : m_Data(data) {}
};

A_11 a(new int(10));
A_11 b(a); // 복사 생성자 호출
A_11 c(std::move(a)); // 이동 생성자 호출
```

실행 결과 입니다. 잘 호출되는군요.

```cpp
CopyableMoveable_11 : Copy Constructor
CopyableMoveable_11 : Move Constructor
```

하지만, 소멸자를 정의하는 순간 암시적 이동 생성자와 암시적 이동 대입 연산자는 만들어지지 않습니다.

```cpp
class A_11 {
private:
    CopyableMoveable_11<int> m_Data;
public:
    explicit A_11(int* data) : m_Data(data) {}
    ~A_11() {} // 암시적 이동 생성자와 암시적 이동 대입 연산자가 만들어 지지 않습니다.
};

A_11 a(new int(10));
A_11 b(a); // 복사 생성자 호출
A_11 c(std::move(a)); // (△) 비권장. 복사 생성자 호출
```

실행 결과 입니다. 이동 연산은 복사 연산으로 소리 소문없이 바껴버렸습니다. 성능 개선을 꿈꾸고 `CopyableMoveable_11`을 공들여 만들었지만, 물거품이 되버렸습니다. 문제는 이러한 변경을 컴파일러가 감지해 주지 않는다는 겁니다. 

```cpp
CopyableMoveable_11 : Copy Constructor
CopyableMoveable_11 : Copy Constructor
```

따라서 이동 연산을 사용하는 클래스라면 암시적 버전을 그대로 사용하더라도, 향후 리팩토링시 발생하는 사이드 이펙트를 없애기 위해 미리 `default`를 이용하여 명시적으로 표기하는게 좋습니다. 단, [이동 연산에 따른 암시적 정의](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#%EC%9D%B4%EB%8F%99-%EC%97%B0%EC%82%B0%EC%97%90-%EB%94%B0%EB%A5%B8-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%A0%95%EC%9D%98)의 언급에서 처럼 이동 생성자나 이동 대입 연산자가 정의되면 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)와 [복사 대입 연산자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-assignment-operator/#%EB%B3%B5%EC%82%AC-%EB%8C%80%EC%9E%85-%EC%97%B0%EC%82%B0%EC%9E%90)도 정의해야 합니다.

```cpp
class A_11 {
private:
    CopyableMoveable_11<int> m_Data;
public:
    explicit A_11(int* data) : m_Data(data) {}
    A_11(A_11&) = default; // 암시적 복사 생성자를 사용합니다.
    A_11(A_11&&) = default; // 암시적 이동 생성자를 사용합니다.
    ~A_11() {} // 암시적 이동 생성자와 암시적 이동 대입 연산자가 만들어 지지 않습니다.
};

A_11 a(new int(10));
A_11 b(a); // 복사 생성자 호출
A_11 c(std::move(a)); // 이동 생성자 호출
```
실행 결과는 다음과 같습니다.

```cpp
CopyableMoveable_11 : Copy Constructor
CopyableMoveable_11 : Move Constructor
```

# move()

개체의 타입이 `T` 라고 할때

1. `T&`는 개체의 좌측값 참조라 하고,
2. `T&&`는 개체의 우측값 참조라고 합니다.

[move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)는 좌측값(`lvalue`) 을 우측값(`rvalue`)으로 형변환 합니다. 

다음처럼 [static_cast](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)로도 형변환 할 수 있으나, 가독성을 위해 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 사용합니다. 

```cpp
T t;
static_cast<T&&>(t); // (△) 비권장. 가독성이 떨어집니다.
std::move(t); // (O) 이동 연산을 위해 rvalue로 변환합니다.
```

다음은 `Func_11()`을 `lvalue`와 `rvalue`를 오버로드 한 예입니다. `lvalue`인지 `rvalue`인지에 따라 다른 버전이 호출 됩니다.

```cpp
class A{};
class T {
public:
    static int Func_11(A&) {return 1;} // #1. lvalue
    static int Func_11(A&&) {return 2;} // #2. rvalue
};

A a;

EXPECT_TRUE(T::Func_11(a) == 1); // 이름이 있는 lvalue
EXPECT_TRUE(T::Func_11(static_cast<A&&>(a)) == 2); // 강제 형변환 해서 rvalue
EXPECT_TRUE(T::Func_11(std::move(a)) == 2); // lvalue를 move 해서 rvalue

A& b = a;
EXPECT_TRUE(T::Func_11(b) == 1);  
EXPECT_TRUE(T::Func_11(static_cast<A&&>(b)) == 2);
EXPECT_TRUE(T::Func_11(std::move(b)) == 2);

EXPECT_TRUE(T::Func_11(A()) == 2); // 임시 개체는 rvalue
EXPECT_TRUE(T::Func_11(std::move(A())) == 2); // 임시 개체를 move 해도 rvalue
```

# move_if_noexcept()

`move_if_noexcept()` 는 [nothrow 보증](https://tango1202.github.io/classic-cpp-exception/classic-cpp-exception-warranty/#%EC%98%88%EC%99%B8-%EB%B3%B4%EC%A6%9D-%EC%A2%85%EB%A5%98)이 되는 경우에만 `&&`로 형변환 합니다. 

다음 코드를 보면 `A_11`는 예외 보증이 안되어 있고, `B_11`는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 지정되어 있는데요,

1. `A_11`의 경우는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 없어 [복사 생성자](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-constructors/#%EB%B3%B5%EC%82%AC-%EC%83%9D%EC%84%B1%EC%9E%90)가 호출되고,
2. `B_11`의 경우는 [noexcept](https://tango1202.github.io/mordern-cpp/mordern-cpp-noexcept/)가 있어 이동 생성자가 호출됩니다.

```cpp
class A_11 {
public:
    A_11() {}
    // noexcept가 없어서 예외가 발생할 수도 있습니다.
    A_11(const A_11&) {std::cout << "A_11 : Copy Constructor" << std::endl;}
    A_11(A_11&&) {std::cout << "A_11 : Move Constructor" << std::endl;;} 
};
class B_11 {
public:
    B_11() {}
    // nothrow 보증합니다.
    B_11(const B_11&) noexcept {std::cout << "B_11 : Copy Constructor" << std::endl;;}
    B_11(B_11&&) noexcept {std::cout << "B_11 : Move Constructor" << std::endl;;} 
};

A_11 a1;
A_11 a2 = std::move_if_noexcept(a1); // A_11(A_11&&)가 nothrow 예외 보증이 되지 않아 그냥 A_11(const A_11&)를 호출합니다.

B_11 b1;
B_11 b2 = std::move_if_noexcept(b1); // B_11(B_11&&)가 nothrow 예외 보증이 되어 B_11(B_11&&)를 호출합니다.
```

# forward()

[인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)의 값 카테고리를 유지하며 다른 함수에 전달합니다.

우측값(`rvalue`)을 `T&& val`와 같이 [함수의 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 전달받는 순간, 이름을 갖게 되어 좌측값(`lvalue`)이 되버립니다. 이름이 있으니, 그 이름을 가지고 다른데 사용할 수 있어서 더이상 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 아닌 거죠. 

다음 예에서

1. `g_11(std::move(t))`와 같이 우측값 참조를 사용하여 `g(T&& val)`가 호출되지만,
2.  `f_11(val) + 2`에서 `val`은 좌측값이기 때문에 `f(T& val)`을 호출합니다.

```cpp
class T {};

int f_11(T& val) {return 10;}
int f_11(T&& val) {return 20;}

int g_11(T& val) {return f_11(val) + 1;}
int g_11(T&& val) {return f_11(val) + 2;} // 여기서 val은 이름이 있으니 우측값이 아닙니다. 그래서 f_11(T& val)을 호출합니다.

T t;

// g_11(T& val)와 f_11(T& val)가 호출되어 11입니다.
EXPECT_TRUE(g_11(t) == 11); 

// g_11(T&& val)와 f_11(T& val)가 호출되어 12입니다.
EXPECT_TRUE(g_11(std::move(t)) == 12);
```

우측값 참조(`&&`)가 좌측값 참조(`&`) 로 변경되어 버렸습니다.([이름이 부여된 우측값](??) 참고) 이해는 됩니다. `val`로 명명 되는 순간 더이상 [임시 개체](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%9E%84%EC%8B%9C-%EA%B0%9C%EC%B2%B4)가 아니니까요. 이를 해결하려면 다시 한번 [move()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#move)를 사용하여 우측값으로 바꿀 수도 있으나, 값 카테고리를 유지하여 전달한다는 의미로 [forward()](https://tango1202.github.io/mordern-cpp/mordern-cpp-rvalue-value-category-move/#forward)를 사용하는게 좋습니다.

```cpp
class T {};

int f_11(T& val) {return 10;}
int f_11(T&& val) {return 20;}

int g_11(T& val) {return f_11(val) + 1;}
// int g_11(T&& val) {return f_11(std::move(val)) + 2;} // (△) 비권장. 이동 연산을 수행한다는 느낌이어서 가독성이 떨어집니다.
int g_11(T&& val) {return f_11(std::forward<T>(val)) + 2;} // 값 카테고리를 유지한채 전달한다는 의미로 forward 사용

T t;

// g_11(T& val)와 f_11(T& val)가 호출되어 11입니다.
EXPECT_TRUE(g_11(t) == 11); 

// g_11(T&& val)와 f_11(T&& val)가 호출되어 22입니다.
EXPECT_TRUE(g_11(std::move(t)) == 22);
```

# 이름이 부여된 우측값

[우측값 참조](??)에 이름이 부여되면 좌측값입니다. 이름이 부여된 순간 [임시 개체](??)가 아니니까요. 즉 다음 `ref_11`은 [우측값을 참조](??)하는 좌측값입니다.

```cpp
A val;
A&& ref_11 = std::move(val);
```

따라서 모든 [인자](??)는 이름이 있으므로 좌측값입니다.

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

int& r = val;
int& & rr = r; // (X)



참조자 끼리 중복될때의 규칙

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