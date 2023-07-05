---
layout: single
title: "#11. [고전 C++ 가이드] 상수(const), 변경 가능 지정자(mutable), 최적화 제한 지정자(volatile)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `const` 정확성을 지켜라.
> * 무조건 `const`를 들이대라.
> * `const` 변수와 `const` 함수는 메모리를 수정하지 않기 때문에 예외가 발생하지 않는다.

# 개요

|항목|내용|
|--|--|
|상수 개체|개체 정의시 `const`를 붙여 상수 개체를 만들 수 있습니다. 상수 개체는 값을 변경할 수 없습니다.<br/>생성시 초기화 해야 합니다.|
|상수 멤버 함수|멤버 함수의 뒤에 `const`를 붙여 상수 멤버 함수를 만들 수 있습니다.<br/>상수 멤버 함수는 개체의 멤버 변수를 변경할 수 없습니다.<br/>단, `mutable`로 정의된 개체는 수정 가능합니다.|

`const`인 개체나 함수를 사용하면, 메모리의 수정이 없으므로 예외에 안정적입니다. 안정적인 프로그램을 위해, 할 수 있는 한 최대한 많이 `const`로 작성하는 것이 좋습니다.

다만, 하기 **상수성 계약** 을 준수하세요. 예외 발생 부분만 빼고는 대부분 컴파일 오류가 발생하니, 계약을 지키기 까다롭지도 않습니다.

# 상수성 계약

1. 상수 개체는 생성과 함께 초기화 되어야 합니다.(컴파일 오류)
2. 상수 개체는 변경할 수 없습니다.(컴파일 오류)
3. 상수 멤버 함수는 멤버 변수를 수정하지 않습니다.(컴파일 오류, 단 `mutable`로 수정 가능)
4. 상수 멤버 함수는 멤버 변수를 몰래 수정할 수 있는 포인터나 참조자를 리턴하지 않습니다.(컴파일 오류. 단, `const_cast`로 억지로 구현 가능하나 하지 마세요.)
5. 상수 멤버 함수는 내부 구현에서 상수 멤버 함수만을 호출합니다.(컴파일 오류)
6. 상수 멤버 함수는 예외를 발생하지 않습니다.(노력해서 구현해야 함)
7. `mutable`로 [논리적 상수성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/)을 구현한 경우 예외를 발생하지 않아야 합니다.(노력해서 구현해야 함)


# 상수 변수

상수 변수는 `const`를 붙여 정의합니다. 반드시 초기값을 설정해야 합니다. 

```cpp
const int x; // (X) 컴파일 오류. 초기값 없음
const int x = 10; // (O) x의 값은 이제 변경될 수 없음
```

포인터(혹은 참조자)형 변수의 경우 `const*`와 같이 `const`의 오른쪽에 `*`이 있으면 포인터가 참조하는 실제 데이터가 `const` 이고, 왼쪽에 `*`이 있으면 포인터형 변수가 `const`입니다.([포인터(Pointer)와 참조자(Reference)](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-pointer-reference/) 언급)

```cpp
int obj = 10;
int* p1 = &obj; // *p1 수정 가능. p1 수정 가능
*p1 = 20;

const int* p2 = &obj; // *p2 수정 불가. p2 수정 가능
*p2 = 20; // (X) 컴파일 오류
p2 = p1;

int* const p3 = &obj; // *p3 수정 가능. p3 수정 불가
*p3 = 20;
p3 = p1; // (X) 컴파일 오류

const int* const p4 = &obj; // *p4 수정 불가. p4 수정 불가
*p4 = 20; // (X) 컴파일 오류
p4 = p1; // (X) 컴파일 오류
```

# 리턴값의 상수성

리턴값에 무의미하게 `const`를 붙일 필요는 없습니다. 의미에 맞게 붙이거나 떼야 합니다.

리턴값이 기본 타입이라면 어짜피 복제되어 리턴되므로, `const`를 붙일 필요가 없습니다.

```cpp
// (O) 멤버 변수의 값을 리턴하는 const 함수
int GetX1() const {return m_X;} 

// (△) 비권장. 리턴값을 쓸데없이 const로 리턴하는 const 함수. 
const int GetX2() const {return m_X;} 
```

멤버 변수의 포인터를 리턴하는 경우, `const` 함수인지 아닌지에 따라 포인터 상수성을 맞춰서 리턴해야 합니다. `GetX4()`처럼 맞지 않게 리턴하면, **상수성 계약** 위반입니다.(억지로 `const_cast`를 하지 마세요.)

```cpp
// (O) 멤버 변수의 값을 수정하지 않는 const 함수
const int* GetX3() const {return &m_X;}    

// (△) 비권장. 멤버 변수의 값을 몰래 수정할 수 있는 const 함수
int* GetX4() const {return const_cast<int*>(&m_X);}

// (O) 맴버 변수의 값을 수정하는 none-const 함수      
int* GetX5() {return &m_X;} 	                        
```

# 인자(함수 선언에 작성된 Parameter)의 상수성

인자에 `const`를 사용하여 전달된 인수(함수에 전달하는 개체. Argument)를 함수가 수정하는지, 수정하지 않는지 **코딩 계약**을 만들어 주는 게 좋습니다. 

전달된 인수를 함수에서 수정하지 않는다면, 꼭 `const`로 만들어 주길 바랍니다.(단, 인수가 인자에 복사된다면, `const`는 무의미 하므로 사용할 필요가 없습니다. 또한 [오버로딩된 함수 결정 규칙](https://github.com/tango1202/tango1202.github.io/blob/main/_posts/2019-01-19-classic-cpp-guide-function.md#%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9%EB%90%9C-%ED%95%A8%EC%88%98-%EA%B2%B0%EC%A0%95-%EA%B7%9C%EC%B9%99)을 참고하면, 오버로딩 결정시 `f(const int x)` 는 `f(int x)`로 취급되는걸 알 수 있습니다.)

```cpp
void f(int x); // (O) 인수를 x에 복사해서 사용함.
void f(const int x); // (△) 비권장. 인수를 x에 복사해서 쓰되 f에서 수정하지 않음. 호출하는 쪽에선 무의미

void f(int* x); // (O) 인수를 f에서 수정함.
void f(int& x); 

void f(const int* x); // (O) 인수를 f에서 수정하지 않음.
void f(const int& x);   
```
# 상수 멤버 함수

멤버 함수의 뒤에 `const`를 붙여 상수 멤버 함수를 만들 수 있습니다. 상수 멤버 함수는 개체의 멤버 변수를 변경할 수 없습니다.([멤버 함수, 상수 멤버 함수, 논리적 상수성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/) 참고)

```cpp
class T {
private:
    int m_Val;
public:
    void f() const { // 상수 멤버 함수입니다.
        m_Val = 10; // (X) 컴파일 오류. 상수 멤버 함수에서 멤버 변수를 수정할 수 없습니다.
    }
};
```

# 변경 가능 지정자(`mutable`)

지연 생성이나 캐쉬등 [논리적 상수성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/)인 상수 함수에서 멤버 변수를 수정해야 할 때 사용합니다.

```cpp
TEST(TestClassicCpp, Mutable) {
    class T {
    public:
        // 메모리를 절약하기 위해 GetString() 으로 실제 데이터를 요청할때 문자열을 채울겁니다.
        mutable std::wstring m_Lazy; // const 함수에서 수정할 수 있습니다.
        
        // 개념적으로 내부 String을 리턴하므로 const 함수
        // 하지만 내부에서 m_Lazy를 세팅하기 때문에 mutable을 사용합니다.
        const std::wstring& GetString() const {
            m_Lazy = L"Lazy String";
            return m_Lazy;
        }
    };
    T t;
    EXPECT_TRUE(t.GetString() == L"Lazy String");
}
```

# 최적화 제한 지정자(`volatile`)

컴파일러 최적화 옵션을 비활성 시킵니다.

디바이스에 명령을 내리기 위해, 특정 메모리 공간에 쓰기 명령을 주어야 한다고 합시다.

```cpp
unsigned int *p = 0x1234;
*p = 0x0001;
*p = 0x0002;
*p = 0x0003;
```

`*p` 에 쓰기 명령을 주어 3개의 명령을 수행합니다.

하지만, 어차피 `*p`에 값을 덮어쓰다가 결국 `0x0003`이 되니, 컴파일러는 최종값만 저장하는 것으로 최적화 할 수 있습니다.

```cpp
unsigned int *p = 0x1234;
*p = 0x0003; // 최종값만 저장합니다.
```

이런 최적화를 막기 위해 `volatile`을 사용합니다.

```cpp
volatile unsigned int *p = 0x1234; // 컴파일러 최적화를 하지 않습니다.
*p = 0x0001;
*p = 0x0002;
*p = 0x0003;
```



