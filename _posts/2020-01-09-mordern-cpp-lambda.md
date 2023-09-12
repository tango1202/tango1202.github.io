---
layout: single
title: "#9. [모던 C++] (C++11~) 람다 표현식, 클로져"
categories: "mordern-cpp"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 람다 표현식이 추가되어 1회용 익명 함수를 만들 수 있습니다. 
 
# 개요

C++11에서는 함수 지향 프로그래밍을 위해 람다 표현식을 추가하였습니다. 람다 표현식은 `prvalue` 타입의 1회용 익명 함수를 만들며, 이를 **클로져(Closure)**라고 합니다. 

람다 표현식은 STL의 알고리즘과 잘 호환될 뿐만 아니라, 캡쳐 기능등의 활용도도 높아서 복잡한 함수자를 만들기 보다는 람다 표현식을 사용하는게 훨씬 좋습니다.

람다 표현식의 기본 문법은 다음과 같습니다.

`[캡쳐](인자들) -> 리턴 타입 {표현식}`

예를 들면,

```cpp
[](int a, int b) -> int {return a + b;}
```

이는 다음의 함수 정의와 같습니다.

```cpp
int f(int a, int b) {
    return a + b;
}
```

인자나 리턴값이 없다면 다음과 같이 생략 할 수 있습니다.

```cpp
[] {std::cout<<"lambda"<<std::endl;}
```

# 람다 함수 호출

일반 함수를 호출하려면 `f(10, 20)` 와 같이 호출하는데요, 람다 표현식으로 작성된 람다 함수를 호출하려면 다음과 같이 합니다.

1. 람다 표현식으로부터 생성된 개체(이를 **클로져**라 합니다.)를 변수에 저장한뒤 호출할 수 있고,

    ```cpp
    auto f = [](int a, int b) -> int {return a + b;};
    int val = f(10, 20); // 함수 호출하듯이 f(10, 20) 로 호출합니다.
    EXPECT_TRUE(val == 30);
    ```

2. 클로져 개체에 `()`을 붙여 바로 호출할 수 있습니다.

    ```cpp
    int val = [](int a, int b) -> int {return a + b;}(10, 20); // 클로져 개체에 (10, 20)을 붙여 호출합니다.
    EXPECT_TRUE(val == 30);
    ```

# 캡쳐

람다 표현식 바깥 부분의 개체 정보들을 람다 표현식 내부에 전달합니다.

|항목|내용|
|--|--|
|`[=]`|외부의 모든 변수를 `const`형 값으로 가져옵니다.|
|`[&]`|외부의 모든 변수의 참조자를 가져옵니다.|
|`[=, &x, &y]`|외부의 모든 변수를 값으로 가져오되 `x`, `y` 만 참조로 가져옵니다.|
|`[&, x, y]`|외부의 모든 변수의 참조자를 가져오되 `x`, `y` 만 값으로 가져옵니다.|
|`this`|람다 표현식을 사용한 개체의 `this` 포인터를 값으로 가져옵니다.|

다음 코드에서는 람다 표현식 외부에 정의된 `sum`을 캡쳐하고, 람다 표현식 내에서 `v`의 각 요소의 값을 전달받아 `sum`에 누적합니다.

```cpp
int sum = 0;
std::vector<int> v{1, 2, 3};

std::for_each( // 시퀀스 안의 요소들에 대해 f(요소)를 실행합니다. f는 람다 함수입니다.
    v.begin(), 
    v.end(),
    [&sum](int val) {sum += val;} // 캡쳐된 sum에 시퀀스 요소의 값(val)을 누적합니다.
);

EXPECT_TRUE(sum == 1 + 2 + 3);
```

**캡쳐 시기**

람다 표현식은 클로져 개체가 생성될 때 캡쳐를 합니다. 따라서, 값으로 개체를 캡쳐할 때 클로져 개체를 생성한 시점과 호출한 시점이 다르면, 값이 다를 수 있습니다.

```cpp
int val = 1;
auto f = [=]() -> int {return val;}; // 클로져 개체가 생성되는 시점에 val을 캡쳐합니다.
val = 2;

EXPECT_TRUE(f() == 1); // 캡쳐할 때의 값을 사용하므로 1입니다.
```

**값 캡쳐**

값으로 개체를 캡쳐하면 `const`로 캡쳐하므로 그 값을 수정할 수 없습니다. 다만, 포인터 변수는 `int* const`여서 `ptr`은 수정할 수 없지만, `*ptr`은 수정할 수 있습니다.

```cpp
int a = 1;
int b = 2;
int c = 3;
int* ptr = &b;
int& ref = c;
[=]() { // 값으로 복제하며 `const` 입니다.
    // a = 10; // (X) 컴파일 오류. const 이므로 수정할 수 없습니다.
    // ptr = &a; // (X) 컴파일 오류. const 이므로 수정할 수 없습니다.
    *ptr = 20; // int* const 여서 ptr 은 수정할 수 없지만, ptr이 가리키는 개체는 수정할 수 있습니다.
    // ref = 30; // (X) 컴파일 오류. const 이므로 수정할 수 없습니다.
}();

EXPECT_TRUE(b == 20);         
```

이런 경우 `mutable`을 사용하면 캡쳐한 개체를 수정할 수 있으나, 원본이 아닌 복제본만 수정됩니다.(원본을 수정하려면 참조 캡쳐를 이용하셔야 합니다.)

```cpp
int a = 1;
int b = 2;
int c = 3;
int* ptr = &b;
int& ref = c;
[=]() mutable { // 값으로 캡쳐했지만 수정할 수 있습니다.
    a = 10; // (△) 비권장. 복제본이 수정될 뿐, 원본이 수정되는 건 아닙니다.
    *ptr = 20;
    ref = 30; // (△) 비권장. 복제본이 수정될 뿐, 원본이 수정되는 건 아닙니다.
}();

EXPECT_TRUE( a == 1 && b == 20 && c == 3);         
```

개체의 멤버 함수 내에서 사용할 때에는 값 캡쳐시 `this` 포인터가 복제되어 개체의 내부 멤버 변수를 수정할 수 있습니다.

```cpp
class T {
    int m_Member = 1;
public:
    int GetMember() const {return m_Member;}
    int Func() {
        int local = 2;

        auto f = [=]() -> int {
            m_Member = 10; // 멤버 변수의 값을 수정합니다.
            return m_Member + local;
        };  

        return f();
    }
};

T t;
EXPECT_TRUE(t.Func() == 12);
EXPECT_TRUE(t.GetMember() == 10); // 멤버 변수가 수정되어 있습니다.
```

**참조 캡쳐**

참조 캡쳐를 이용하면, 캡쳐한 개체를 수정할 수 있습니다.

```cpp
int a = 1;
int b = 2;
int c = 3;
int* ptr = &b;
int& ref = c;
[&]() { // 캡쳐한 개체를 수정할 있습니다.
    a = 10; 
    *ptr = 20;
    ref = 30;
}();

EXPECT_TRUE( a == 10 && b == 20 && c == 30);     
```

# 클로져 개체 저장

클로져 개체를 `auto`를 이용해서 저장했는데요, `auto`외에 함수 포인터와 `std::function`을 이용하는 방법이 있습니다.

1. `auto`(단, `auto`이기 때문에 함수의 인자로 사용할 수 없습니다.)
    
    ```cpp
    auto f = [](int a, int b) -> int {return a + b;};
    EXPECT_TRUE(f(10, 20) == 30);
    ```

2. 함수 포인터(단, 캡쳐를 지원하지 않습니다.)
   
    ```cpp
    typedef int (*Func)(int, int); // 함수 포인터 typedef
    Func f = [](int a, int b) -> int {return a + b;};
    EXPECT_TRUE(f(10, 20) == 30);
    ```

3. `std::function`(캡쳐도 지원하고, 함수 인자로 사용할 수도 있습니다.)
    
    ```cpp
    std::function<int(int, int)> f;
    int g(const std::function<int(int, int)>& lambda, int a, int b) { // 함수의 인자로 람다를 지정합니다.
        return lambda(a, b);
    }
    int c = 30;
    f = [=](int a, int b) -> int {return a + b + c;}; // 캡쳐를 사용하는 람다 표현식도 사용할 수 있습니다.      
    EXPECT_TRUE(g(f, 10, 20) == 60); // g() 함수에 클로져 개체를 저장한 f를 전달합니다. 
    ```

# 클로져 개체 복사 부하

람다 표현식에서 값 캡쳐를 사용하면 
1. 캡쳐시에 복제 부하가 있으며, 
2. 클로져 개체간 복사시에도 복제 부하가 있으므로,

참조 캡쳐를 이용하는게 좋습니다.

다음 `T`클래스는 클로져의 캡쳐 방식에 따라 생성자와 소멸자 호출을 탐지하기 위한 테스트 클래스입니다.

```cpp
class T {
public: 
    T() {std::cout<<"T::Default Constructor"<<std::endl;}
    T(const T&) {std::cout<<"T::Copy Constructor"<<std::endl;}
    T(T&&) {std::cout<<"T::Move Constructor"<<std::endl;}
    ~T() {std::cout<<"T::Destructor"<<std::endl;}
    void operator =(const T&) {std::cout<<"T::operator =()"<<std::endl;}
};
```

1. 값 캡쳐던, 참조 캡쳐던, 해당 개체를 람다 표현식에서 사용하지 않으면 복사하지 않습니다.

    ```cpp
    T t;
    [=]() {std::cout<<"Run Lambda"<<std::endl;}(); // t를 사용하지 않았습니다.
    ```
    ```cpp
    T::Default Constructor
    Run Lambda
    T::Destructor
    ```

2. 값 캡쳐를 사용하면, 대상 개체가 람다 표현식에 사용될때, 복사 생성자를 호출하여, `const` 복제본을 만듭니다.

    ```cpp
    T t;
    [=]() {t; std::cout<<"Run Lambda"<<std::endl;}(); // t;로 사용합니다. 캡쳐시 복사 부하가 있습니다.       
    ```

    ```cpp
    T::Default Constructor
    T::Copy Constructor // 캡쳐시 복사 생성자를 호출하여 const 복제본을 만듭니다.
    Run Lambda
    T::Destructor // 복제본을 삭제합니다.
    T::Destructor    
    ```

3. 값 캡쳐를 사용하더라도, `auto` 변수에 저장하고 호출하는 것은 추가의 복사 부하가 없습니다.

    ```cpp
    T t;
    auto f = [=]() {t; std::cout<<"Run Lambda"<<std::endl;}; // auto 변수에 저장하고, 호출합니다.        
    f();
    ```
    ```cpp
    T::Default Constructor
    T::Copy Constructor // 추가 복사 부하가 없습니다.
    Run Lambda
    T::Destructor
    T::Destructor
    ```

4. 값 캡쳐를 사용할때 `auto` 변수들 끼리 복제하면 추가의 복사 부하가 발생합니다.

    ```cpp
    T t;
    auto f1 = [=]() {t; std::cout<<"Run Lambda"<<std::endl;}; 
    auto f2 = f1; // 복사 부하가 발생합니다.
    f1();
    f2();
    ```

    ```cpp
    T::Default Constructor
    T::Copy Constructor // 캡쳐시 복사 생성자를 호출하여 const 복제본을 만듭니다.
    T::Copy Constructor // f2 = f1시 복제본을 만듭니다. 추가 복사 부하가 있습니다.
    Run Lambda
    Run Lambda
    T::Destructor
    T::Destructor
    T::Destructor
    ```

5. 참조 캡쳐를 사용하면 복사 부하가 없습니다.

    ```cpp
    T t;
    auto f1 = [&]() {t; std::cout<<"Run Lambda"<<std::endl;}; // 캡쳐시 복사 부하가 없습니다.
    auto f2 = f1; // 대입시 복사 부하가 없습니다.
    f1();
    f2();      
    ```
    ```cpp   
    T::Default Constructor
    Run Lambda
    Run Lambda
    T::Destructor
    ```
