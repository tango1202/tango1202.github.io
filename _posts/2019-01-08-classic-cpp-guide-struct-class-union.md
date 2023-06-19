---
layout: single
title: "#8. [고전 C++ 가이드] 구조체(Struct)와 클래스(Class) 와 공용체(Union)"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 구조체와 클래스의 차이는 초기화 방법과 기본 엑세스 지정자 뿐이다.
> * 공용체는 플랫폼에 따라, 컴파일러에 따라, 최적화 옵션에 따라 예기치 못한 동작을 할 수도 있으니 꼭 필요한 경우 한정적으로 사용하라.

**개요**

|항목|구조체|클래스|공용체|
|--|--|--|--|
|용량|멤버 변수들의 총합|멤버 변수들의 총합|멤버 변수들중 가장 큰값|
|초기화|`T t = {10, 20};` 지원|값 초기화 생성자|X|
|기본 엑세스 지정자|`public`|`private`|`public`|
|멤버 변수|O|O|O|
|멤버 함수|O|O|O|
|참조자 멤버 변수|O|O|X|
|재정의 생성자|O|O|X|
|재정의 소멸자|O|O|X|
|virtual 함수|O|O|X|
|상속|O|O|X|

구조체와 클래스와 공용체는 타입(자료형)이 다른 여러 데이터를 집합으로 묶어 관리할 수 있게 해줍니다.

특히, 구조체와 클래스는 초기화 방법과 기본 엑세스 지정자 외에는 모두 동일하고, [캡슐화](https://tango1202.github.io/principle/principle-encapsulation/)를 위한 다양한 기능들을 제공합니다.

```cpp
struct S {
    int x; // 기본적으로 public
    int y;
};

S s = {10, 20}; // 중괄호 초기화 지원

class C {
    int x; // 기본적으로 private
    int y;
public:
    C(int x, int y) {} // 값 생성자 정의
};
C c(10, 20); // 값 생성자를 재정의 해서 사용
```

공용체는 멤버 변수들끼리 메모리 영역을 공유합니다. 이에 따라 참조자나 캡슐화를 위한 다양한 기능들이 제약됩니다. 메모리 절약이 필요할 경우에만 한정적으로 사용하시기 바랍니다.

**구조체와 클래스**

1. 전방 선언

    서로 다른 구조체나 클래스가 상호 참조할 경우, 컴파일이 안될 수도 있습니다.
    
    ```cpp
    class MyClass {
        YourClass your; // (X) 컴파일 오류. YourClass가 뭔지 모릅니다.
    };
    class YourClass {
        MyClass my; // MyClass는 상위에 정의되어 사용할 수 있습니다.
    }; 
    ```
    
    이런 경우, 하기와 같이 전방 선언으로 알려주고 컴파일 할 수 있습니다. 이때 구체 정의가 아닌 포인터나 참조자 형식이어야 합니다. 향후 실제 정의(클래스 멤버 접근등)를 사용하려면, 헤더 파일에는 클래스 선언을 하고, cpp 파일에 클래스 정의를 하여 사용하셔야 합니다.

    ```cpp
        class YourClass; // 전방 선언
        class MyClass {
            // (O) 전방 선언을 통해 YourClass가 대충 클래스라는 걸 압니다. 
            // 반드시 포인터나 참조자와 같은 참조 형식이어야 합니다.
            // 구체 정의를 사용하려면, 헤더 파일과 cpp 파일을 분리하고, cpp 부분에서 구체 정의를 사용하세요.
            YourClass* your; 

            void f(); // 선언만 하고, cpp에서 yourClass를 사용할 겁니다.
        };
        class YourClass {
            MyClass my; // MyClass는 상위에 정의되어 사용할 수 있습니다.

            void f() {}
        }; 
    ```
    
    ```cpp
        // cpp 파일에서 YourClass의 구체 정의 사용
        void MyClass::f() {
            your->f(); 
        }
    ```
2. 멤버 사양
   
   구조체와 클래스의 멤버는 멤버 변수와 멤버 함수 뿐만아니라, 열거형 상수, 중첩 클래스(구조체), 자료형 재정의(typdef)를 포함할 수 있습니다.

    ```cpp
    class T {
        int d1; // 비정적 데이터 멤버
        static const int d2 = 1; // 정적 데이터 멤버. 단 nested 클래스에서는 사용 못함
    
        virtual void f1(int) = 0; // 순가상 함수
        virtual void f2(int) {} // 가상 함수    
        void f3(int) {} // 함수
        static void f4(int) {} // 정적 함수
    
        enum {left, top, right, bottom}; // 열거형 상수
    
        class NestedClass { // 중첩 클래스 혹은 구조체
            int myData;
        };
        typedef NestedClass Inner; // 형식 재정의
    };
    ```

3. inline 함수

    클래스(구조체) 선언부에 정의한 함수는 `inline` 화 됩니다.

    ```cpp
    // h에서
    class T {
        int f1() {} // inline화 됨
        int f2();
    };

    // cpp에서
    int T::f2() {} // inline화 안됨
    ```

4. 엑세스 지정자
 
    엑세스 지정자를 이용하여 외부 접근을 통제할 수 있습니다.

    |항목|내용|
    |--|--|
    |`public`|외부 접근 허용|
    |`protected`|상속받은 자식 개체에만 허용|    
    |`private`|자기 자신만 사용|

5. Using 선언

    `using`을 사용하면 부모 클래스에서 지정한 엑세스 지정자를 강제로 변경할 수 있습니다만, 사용하지 마세요. 부모에서 설정한 행동을 임의로 바꾸는 건 좋지 않습니다.([리스코프 치환 원칙](https://tango1202.github.io/principle/principle-liskov-substitution/) 참고)
    
    ```cpp
    class Base {
    protected:
        int val;
    };
    
    class Derived : public Base {
    public:
        using Base::val; // Base val을 public으로 변경합니다.
    };
    Derived d;
    d.val = 10; // 이제 public이라 접근 가능합니다.
    ```

6. 함수 내부의 로컬 클래스

    함수 내부에 클래스(구조체)를 정의하여, 함수 내부에서만 한정해서 사용할 수 있습니다.
    ```cpp
    void f() {
        class T {
        public:
            int x;
        };
        T t; // 함수내에서만 사용 가능
        t.x = 10; 
    }
    ```

**공용체**

공용체는 멤버 변수들끼리 메모리 영역을 공유하다 보니 하나의 멤버 변수를 수정하면, 다른 멤버 변수도 값이 수정된 효과를 볼 수 있습니다. 그러나, 자료형의 크기나 메모리 정렬이 바뀌면(플랫폼에 따라, 컴파일러에 따라, 최적화 옵션에 따라) 오동작을 할 수 있으니, 주의해서 사용해야 합니다.

```cpp
class C {
private:
    int val1;
    int val2;
public:
    // C(int v1, int v2) {} // (X) 컴파일 오류. 공용체 멤버는 생성자를 가질 수 없음
    // ~C() {} // (X) 컴파일 오류. 공용체 멤버는 소멸자를 가질 수 없음
    int GetVal1() const { return val1; } // 멤버 함수가 있어도 되나, virtual 이면 안됨
    void SetVal1(int val) { val1 = val; }
};

struct S1 {
    int x;
    int y;   
};
struct S2 {
    int x;
    int y;
    int z;        
};    
union U {
    C c; // 멤버 변수들끼리 메모리를 공유함
    S1 s1;
    S2 s2;
public:
    int GetX() const { return s1.x; } // 공용체도 함수를 가질 수 있음 
};

U u;
EXPECT_TRUE(sizeof(u) == sizeof(S2)); // 공용체 멤버 변수중 용량이 가장 큰 개체

u.s1.x = 10; // s1을 바꿨지만, c와 s2도 변경됩니다.
EXPECT_TRUE(u.GetX() == 10);
EXPECT_TRUE(u.c.GetVal1() == 10);
EXPECT_TRUE(u.s2.x == 10);
   

u.c.SetVal1(20); // c를 바꿨지만, s1과 s2도 변경됩니다.
EXPECT_TRUE(u.c.GetVal1() == 20);
EXPECT_TRUE(u.s1.x == 20);
EXPECT_TRUE(u.s2.x == 20);
```
