---
layout: single
title: "#7. [고전 C++ 가이드] 포인터(Pointer)와 참조자(Reference)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 포인터 보다는 참조자를 사용하라.(널검사가 필요없다.)
> * [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)의 참조자를 리턴하지 마라.(이미 소멸된 개체다.)

# 개요

|항목|포인터|참조자|
|--|--|--|
|정의|개체나 함수의 주소값 저장|개체나 함수의 별칭|
|nullable|가능|불가능|
|용량|32bit : 4byte,<br/>64bit : 8byte|참조하는 개체의 별칭으로서 해당 용량은 스펙에 정의되지 않음.<br/>다만, `sizeof()`시 참조하는 개체와 동일 크기를 리턴하도록 스펙에 정의됨.(`sizeof(T&) == sizeof(T)`)|
|초기화|생성하면서 초기화,<br/>생성후 초기화 가능|생성하면서 초기화만 가능|
|값 변경|다른 포인터로 변경 가능<br/>가리키는 개체의 실제값 변경 가능|다른 참조자로 변경 불가능<br/>참조하는 개체의 실제값 변경 가능|

포인터와 참조자는 대상 개체를 가리키기 위한 타입입니다. 주로 하기 경우에 사용됩니다.

1. 대상 개체가 너무 커서 [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)나 [리턴](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92)시 복사 부하가 너무 큰 경우
2. 원본 데이터를 값을 확인 또는 수정하고 싶은 경우
3. 함수 자체를 전달하고 싶은 경우([함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고)
4. 부모 개체를 통해 자식 개체를 사용하고 싶은 경우([다형성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-virtual-polymorphism/) 참고)

포인터는 `&`을 이용하여 주소값을 얻고, `*`을 이용하여 실제값에 접근합니다.

```cpp
int x = 20;
int* p = &x; // p는 x의 주소를 가집니다.
*p = 30; // p가 가리키는 곳(x)의 실제값이 30이 됩니다.
EXPECT_TRUE(*p == 30 && x == 30); 
```

참조자는 `&`을 이용하여 정의합니다.

```cpp
int x = 20;
int& r = x; // r은 x의 별칭입니다.
r = 30; // r이 참조하는 곳(x)의 실제값이 30이 됩니다.
EXPECT_TRUE(r == 30 && x == 30);
```

# 안정적인 참조자

포인터는 초기화를 하지 않아 쓰레기 값을 가지고 있거나, 널값을 가질 수 있습니다. 그래서 항상 포인터가 유효한 값인지 검사해야 합니다.

이에 반해 참조자는 

1. 생성시 항상 [초기화](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-initialization/) 되어야 하고,
2. 널이 될 수 없으며,
3. 다른 참조로 변경도 안되기 때문에

생성된 참조자는 안심하고 안정적으로 사용할 수 있습니다.

```cpp
int& r; // (X) 컴파일 오류. 생성만 할 수 없습니다. 초기값을 넣어주어야 합니다. 쓰레기값을 가질 일이 없습니다.

int x = 10;
int y = 20;

int& r = x;
r = y; // (O) r 이 y를 참조하는 것이 아니라, y의 값인 20을 대입 받습니다. r이 참조하는 x가 20으로 수정됩니다.
y = 30;
EXPECT_TRUE(x == 20 && r == 20 && y == 30); // r은 x를 참조하기 때문에 y를 30으로 수정한 것과 상관없이 x와 같은 20 입니다.

```

# 널검사가 필요없는 참조자

참조자를 사용하면 널검사가 필요없어 코드가 좀더 간단해 집니다.

```cpp
// 포인터를 인자로 사용하면, 널검사가 필요합니다.
void f(int* p) {
    if (p == NULL) {
        // (△) 비권장. p가 NULL이면 뭘 해야 할까요?
    }
    *p = 20;
}
// 참조자를 인자로 사용하면, 널검사가 필요없습니다.
void f(int& r) {
    r = 20;
}
```

꼭 포인터를 써야만 하는 경우가 아니라면 참조자를 사용하시기 바랍니다.

# Dangling 참조자

[지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)의 참조를 리턴할 경우에는 함수 종료후 소멸된 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)를 참조하게 되므로 비정상 동작합니다. 

이러한 참조자를 Dangling 참조자 라고 합니다.(`const T&`와 같이 상수형 참조를 하면 [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 참조를 리턴해도 사용할 수는 있습니다. 사용할 수는 있어도, [지역 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98) 참조했다는 컴파일 경고도 나오고, 함수 선언도 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 참조를 리턴([Getter](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#getter-%ED%95%A8%EC%88%98) 참고)하는 느낌을 주기 때문에, 안쓰시는게 좋습니다.) 

```cpp
int& GetX() { 
    // (△) 컴파일 경고. 고맙게도 컴파일 경고는 나와줍니다.
    int x = 10; 
    return x; 
} 

// (X) 예외 발생. 이미 소멸된 지역 변수를 참조함
int& result = GetX(); 

// (△) 비권장. 사용할 수는 있으나 GetX()에서 지역 변수 참조 리턴 warning 발생
const int& result = GetX(); 
```

또는 널을 저장한 포인터를 억지로 참조하게 해도 예외가 발생합니다.

```cpp
int* p = NULL;
int& r = *p; // (X) 오동작. *p로 NULL 의 개체를 구하는 건 오동작 할 수 있습니다. 
r = 10; // (X) 예외 발생. p가 NULL이라 10을 NULL(0)주소 위치에 넣다가 예외 발생 합니다. 이렇게 사용하시면 안됩니다.
```

# 포인터 사용법

포인터는 개체 포인터, 배열 포인터, 함수 포인터, 다형성 포인터, [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 포인터, [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) 포인터의 형태로, 개체 정의, [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 정의, [함수 리턴](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 정의시 사용합니다. 

**개체 포인터**

`const`의 오른쪽에 `*`이 있으면 포인터가 참조하는 실제 데이터가 `const` 이고, 왼쪽에 `*`이 있으면 포인터형 변수가 `const`입니다.([상수 변수](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EB%B3%80%EC%88%98) 언급)

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

**[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/) 포인터**

[배열](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-array/)을 가리킬때는 배열 자체에 대한 포인터와 배열의 첫번째 요소를 가리킬 수 있습니다. 주로 배열의 첫번째 요소를 가리키는 형태로 사용합니다.

```cpp
int a[2] = {1, 2};

// 배열 자체를 가리키는 포인터
int(* p5)[2] = &a; // int[2]인 배열을 가리키는 포인터 
EXPECT_TRUE((*p5)[0] == 1 && (*p5)[1] == 2); // (*p5) 로 배열 개체 접근

// 배열의 첫번째 요소를 가리키는 포인터
int* p6 = a; // 혹은 int* p6 = &a[0]; 
EXPECT_TRUE(*(p6 + 0) == 1 && *(p6 + 1) == 2);
EXPECT_TRUE(p6[0] == 1 && p6[1] == 2);    
```

**함수 포인터**

함수 포인터를 이용하여 함수 자체를 변수처럼 사용할 수 있습니다.([함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고)

```cpp
void TestFunc(int) {}

void (*p7)(int) = &TestFunc;
void (*p8)(int) = TestFunc; // &TestFunc 와 동일

p7(10); // (*p7)(10); 도 가능. TestFunc 함수 호출 
p8(10); // (*p8)(10); 도 가능. TestFunc 함수 호출        
```

**다형성 포인터**

부모 개체를 이용하여 자식 개체를 다형적으로 사용할때 포인터를 사용합니다.([다형성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-virtual-polymorphism/) 참고)

```cpp
class Base {
public:
    int m_Value;
    virtual void f() {std::cout<<"Base"<<std::endl;}    
};
class Derived : public Base {
public:
    virtual void f() {std::cout<<"Derived"<<std::endl;}    
};
// 다형성 포인터
{
    Derived d;
    Base* p9 = &d; // 부모 클래스 포인터로 자식 클래스 제어
    p9->f(); // Derived 출력
}
```

**멤버 변수 포인터, 멤버 함수 포인터**

라이브러리 구성시 [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 포인터나 [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) 포인터를 사용할 수 있습니다.([함수 포인터](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0) 참고)

```cpp
// 멤버 변수 포인터 - 라이브러리 개발시 사용할 수도 있음
{
    int Base::* p10 = &Base::m_Value; // Base 클래스 멤버 변수 m_Value를 가리킴
    Base b;
    b.*p10 = 10;
    EXPECT_TRUE(b.m_Value == 10);
}
// 멤버 함수 포인터 - 라이브러리 개발시 사용할 수도 있음
{
    void (Base::* p11)() = &Base::f; // Base 클래스 멤버 함수 f를 가리킴
    Base b;
    (b.*p11)(); // 멤버 함수 포인터는 괄호 필요. Base 출력
}  
```

# 참조자 사용법

참조자는 개체 참조자, 배열 참조자, 함수 참조자, 다형성 참조자, [멤버 변수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-variable/) 참조자, [멤버 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-const-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98) 참조자의 형태로, 개체 정의, [함수 인자](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter) 정의, [함수 리턴](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-function/#%EB%A6%AC%ED%84%B4%EA%B0%92) 정의시 사용합니다. 포인터와는 달리 널을 사용할 수 없습니다. 

**개체 참조자**

```cpp
int obj = 10;
int& r1 = obj; // r1으로 obj 수정 가능
r1 = 20; 

const int& r2 = obj; // const 형이어서 r2으로 obj 수정 불가
r2 = 20; // (X) 컴파일 오류

int& r3 = 20; // (X) 컴파일 오류. T& 로 상수 참조 불가
const int& r4 = 20; // (O) const T&로 상수 참조 가능
```

**배열 참조자**

```cpp
int a[2];
int(& r5)[2] = a; // 괄호로 감쌈
r5[0] = 10;
EXPECT_TRUE(a[0] == 10);
```

**함수 참조자**

```cpp
void (&r6)(int) = TestFunc;
r6(10); // TestFunc 함수 호출 
```

**다형성 참조자**

```cpp
class Base {
public:
    int m_Value;
    virtual void f() {std::cout<<"Base"<<std::endl;}    
};
class Derived : public Base {
public:
    virtual void f() {std::cout<<"Derived"<<std::endl;}    
};
// 다형성 참조자
{
    Derived d;
    Base& r7 = d; // 부모 클래스 참조자로 자식 클래스 제어
    r7.f(); // Derived 출력
}
```
