---
layout: single
title: "#1. [고전 C++ STL] 일반화 프로그래밍"
categories: "classic-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 일반화 프로그래밍이 적합한 코드 구조를 억지로 인터페이스화 하지 마라. 

# 개체 지향 프로그래밍

개체 지향은 데이터와 이를 처리하는 기능을 응집하여 캡슐화하고, 이들을 추상화한뒤 상속하여 다형적으로 동작할 수 있도록 합니다.

|항목|내용|
|--|--|
|[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)|데이터와 기능을 응집|
|[추상화](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/)|공통적인 일반 개념화|
|[상속](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/)|기존 개체의 재활용을 통한 확장|
|[다형성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-polymorphism/)|공통 개념의 다양한 동작|


# 일반화 프로그래밍

일반화 프로그래밍은 타입에 기반(타입 기반 프로그래밍, 타입으로 동작 방식 결정)하여, 다형적 동작을 하는 프로그래밍 방법입니다.

우선, 데이터를 저장하는 컨테이너를 개체 지향스럽게 만들어 봅시다.

1. 캡슐화를 위해 멤버 변수와 멤버 함수를 응집하고,
2. 데이터 조회/설정을 추상화 하여 `IContainer` 인터페이스를 만들고,
3. 이를 상속하여 실제 저장할 타입에 맞춰 `IntContainer`와 `CharContainer`를 만들면,
4. `int`와 `char`에 따라 다르게 다형적으로 동작하게 됩니다.

```cpp
class IContainer {
public:    
    virtual const void* GetAt(size_t index) const = 0;
    virtual void SetAt(void* data, size_t index) = 0; // (△) 비권장. 모든 포인터를 대충 담을 수 있는 void 포인터를 사용합니다.
};

class IntContainer : public IContainer {
    int m_Data[10];
public:
    virtual const int* GetAt(size_t index) const {return static_cast<const int*>(&m_Data[index]);} // 가상 함수의 리턴값은 int*로 변경할 수 있습니다.
    virtual void SetAt(void* data, size_t index) {m_Data[index] = *static_cast<int*>(data);} // (△) 비권장. 모든 포인터를 대충 담을 수 있는 void 포인터를 사용합니다.
};
class CharContainer : public IContainer {
    char m_Data[10];
public:
    virtual const char* GetAt(size_t index) const {return static_cast<const char*>(&m_Data[index]);} // 가상 함수의 리턴값은 char*로 변경할 수 있습니다.
    virtual void SetAt(void* data, size_t index) {m_Data[index] = *static_cast<char*>(data);} // (△) 비권장. 모든 포인터를 대충 담을 수 있는 void 포인터를 사용합니다.
};

IntContainer intContainer;
int i = 10;
intContainer.SetAt(&i, 5); // (△) 비권장. 포인터라서 사용하기 귀찮습니다.
EXPECT_TRUE(*intContainer.GetAt(5) == 10); // (△) 비권장. 포인터라서 사용하기 귀찮습니다.

CharContainer charContainer;
char c = 'c';
charContainer.SetAt(&c, 5);
EXPECT_TRUE(*charContainer.GetAt(5) == 'c');   
```

보기 싫은 부분이 참 많습니다.

1. `IContainer`에서 `void*` 를 사용합니다. 상속받아 사용할 타입이 `int`일지, `char`일지 모르므로, 그냥 다 되도록 `void*` 로 만들었습니다.
2. `GetAt()`함수가 값을 저장하고, 값을 리턴해야 하는데, 부모 개체에서 `void*`를 사용하니 어쩔 수 없이 `int*`와 `char*`를 사용합니다.([가상 함수](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-member-function/#%EA%B0%80%EC%83%81-%ED%95%A8%EC%88%98)의 리턴값 변경 참고)
3. `SetAt()`함수는 더 심각합니다. 아예 `void*`를 전달받을 수 밖에 없습니다.
4. 내부적으로 `void*` 여서 지저분하게 `static_cast`를 사용합니다.
5. 사용할때도 포인터라서 `&`, `*`을 사용해야 하고, 예제에는 없지만, 사실은 널검사도 해야 합니다.

다형적으로 동작할 수 없는 코드 구조를 억지로 인터페이스화 해서 만들어진 일입니다. 이러지 마세요. 과도한 설계입니다. 차라리 추상화하지 말고 다음처럼 구현하는게 차라리 낫습니다.

```cpp
// (△) 비권장. 훨씬 낫습니다. 그러나, 로직은 중복됩니다.
class IntContainer {
    int m_Data[10];
public:
    const int GetAt(size_t index) const {return m_Data[index];} 
    void SetAt(int data, size_t index) {m_Data[index] = data;} 
};
class CharContainer {
    char m_Data[10];
public:
    virtual char GetAt(size_t index) const {return m_Data[index];}
    virtual void SetAt(char data, size_t index) {m_Data[index] = data;} 
};

IntContainer intContainer;
int i = 10;
intContainer.SetAt(i, 5);
EXPECT_TRUE(intContainer.GetAt(5) == 10);

CharContainer charContainer;
char c = 'c';
charContainer.SetAt(c, 5);
EXPECT_TRUE(charContainer.GetAt(5) == 'c');   
```

훨씬 나아졌습니다만, 대표적인 복사/붙여넣기 코드입니다. `int`와 `char`만 다를뿐 모든 로직이 동일합니다.

이럴 경우 타입에 기반한 일반화 프로그래밍 방법을 사용하면, 중복 코드를 해결할 수 있습니다.

C++언어에서는 개체 타입만 변경하여 클래스나 함수를 생성하는 틀로서 템플릿을 제공하며, 상기 코드는 다음과 같이 템플릿으로 대체될 수 있습니다.

```cpp
// 템플릿 정의. 기존 int, char의 타입명이 T로 대체되어 코딩되었습니다.
template<typename T>
class Container {
    T m_Data[10];
public:
    const T GetAt(size_t index) const {return m_Data[index];} 
    void SetAt(T data, size_t index) {m_Data[index] = data;} 
};

// 템플릿 인스턴스화. T 대신 int 를 사용합니다.
Container<int> intContainer;
int i = 10;
intContainer.SetAt(i, 5);
EXPECT_TRUE(intContainer.GetAt(5) == 10);

// 템플릿 인스턴스화. T 대신 char를 사용합니다.
Container<char> charContainer;
char c = 'c';
charContainer.SetAt(c, 5);
EXPECT_TRUE(charContainer.GetAt(5) == 'c');   
```
일반화 프로그래밍은 캡슐화, 추상화, 다형성의 구현 방식의 철학이 개체 지향 방법과는 다릅니다. 상속을 통한 처리 보다는 일반화된 공통 코드와 이의 특수화로 접근합니다.

|항목|내용|
|--|--|
|개체 지향 프로그래밍|캡슐화를 통해 데이터와 처리 방식을 응집하고, 추상화를 통해 공통의 인터페이스 규약을 만듭니다.|
|일반화 프로그래밍|모든 타입에 적용 가능한 일반화된 코드를 개발합니다. 타입에 따라 동작이 다르거나, 성능의 문제가 있다면, [템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/)를 통해 다형적으로 동작하게 만듭니다.|

개체 지향 특성들은 일반화 프로그래밍에서 다음처럼 구현됩니다. 

|항목|내용|
|--|--|
|[캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)|타입과 이를 처리하는 일반화된 코드를 작성<br>알고리즘을 인자화하여 데이터와 처리방식 분리하고 의존성 주입([의존성 역전 원칙](https://tango1202.github.io/principle/principle-dependency-inversion/) 참고)|
|[추상화](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-abstract-class-interface/)|일반화된 주 템플릿 사용<br/>문제 해결을 위한 알고리즘 일반화(공통의 상황/처리방식에 대한 일반화)|
|[상속](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-inheritance/)|기존 개체의 재활용을 통한 확장|
|[다형성](https://tango1202.github.io/classic-cpp-oop/classic-cpp-oop-polymorphism/)|일반화된 주 템플릿 사용하고, 일반적이지 않은 타입은 [템플릿 특수화](https://tango1202.github.io/classic-cpp-stl/classic-cpp-stl-template-specialization/)를 통해 다형 동작|

