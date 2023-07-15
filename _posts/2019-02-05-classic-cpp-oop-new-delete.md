---
layout: single
title: "#5. [고전 C++ 가이드] 개체 생성과 소멸"
categories: "classic-cpp-oop"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 개체는 `new` - `delete` 쌍으로 생성/소멸 하라.
> * 배열은 `new[]` - `delete[]` 쌍으로 생성/소멸하라.

# 개요

|항목|내용|
|--|--|
|`new`|개체의 메모리를 할당하고, 생성자를 호출합니다.|
|`delete`|개체의 소멸자를 호출하고, 메모리를 해제합니다.|
|`new[]`|배열의 메모리를 할당하고, 각 요소의 생성자들을 호출합니다.|
|`delete[]`|배열 요소의 소멸자들을 호출하고, 메모리를 해제합니다.|
|`new (std::nothrow)`|메모리 할당 실패시 NULL 을 리턴합니다. 단, 생성자에서 예외를 발생하면 해당 예외를 전파합니다.|
|`operator new`|개체의 메모리를 할당합니다.|
|`operator delete`|개체의 메모리를 해제 합니다.|
|`operator new[]`|배열의 메모리를 할당합니다.|
|`operator delete[]`|배열의 메모리를 해제합니다.|
|위치 지정 `new()`|주어진 메모리 위치에 개체를 배치하여 생성자를 호출합니다.|
|`set_new_handler()`|`new`에서 예외 발생시 호출되는 함수입니다.|


# `new` - `delete`와 `new[]` - `delete[]`

개체 생성/소멸 시에는 `new` - `delete`를 이용합니다.

```cpp
class T {
public:
    T() {
        std::cout<<"New-Delete Test : T::T()"<<std::endl;
    }
    ~T() {
        std::cout<<"New-Delete Test : T::~T()"<<std::endl;
    }
};

T* t = new T; // (O) 메모리 할당. 생성자 호출
delete t; // (O) 소멸자 호출후. 메모리 해제
```

실행 결과는 다음과 같습니다.

```cpp
New-Delete Test : T::T()
New-Delete Test : T::~T()
```

개체 생성/소멸 시에는 `new[]` - `delete[]`를 이용합니다.

```cpp
T* arr = new T[3]; // (O) 메모리 할당(sizeof(T) * 3 + 오버헤드). 생성자 3회 호출
delete[] arr; // (O) 소멸자 3회 호출. 메모리 해제(sizeof(T) * 3 + 오버헤드)
```

로 하면 실행 결과는 다음과 같이 배열 요소이 갯수 만큼 생성자와 소멸자가 호출됩니다.

```cpp
New-Delete Test : T::T()
New-Delete Test : T::T()
New-Delete Test : T::T()
New-Delete Test : T::~T()
New-Delete Test : T::~T()
New-Delete Test : T::~T()
```

**배열에서 `delete[]` 를 사용해야 하는 이유**

배열의 메모리는 각 요소를 하나하나 할당하는게 아니라, 한번에 할당합니다. 

1. `int`등 기본 자료형일 경우, 메모리가 `sizeof(int)` * 배열 요소 갯수로 할당됩니다.
2. 클래스인 경우 배열 요소 갯수 만큼 생성자, 소멸자가 호출되어야 하기 때문에, 내부적으로 배열 요소 갯수를 저장하는 오버헤드 공간이 추가되어 할당됩니다.(`sizeof(T)` * 배열 크기 + 오버헤드(4byte 일 수도 있고 8byte 일수도 있습니다.))

`new[]`나 `delete[]`는 오버헤드 공간의 배열 요소 갯수 정보를 참조하여 각 요소의 생성자와 소멸자를 각각 호출합니다.

그러다 보니 배열을 `delete`로 소멸시키면, 소멸자가 1회만 호출되고, 프로그램이 다운될 수 있습니다. `new[]`로 생성한 배열은 꼭 `delete[]`로 소멸시켜야 합니다.

```cpp
T* arr = new T[3]; // (O) 메모리 할당(sizeof(T) * 3 + 오버헤드). 생성자 3회 호출
delete arr; // (X) 예외 발생. 소멸자가 1회만 호출되고, 프로그램이 다운될 수 있음
```

# `operator new`와 `operator delete`

1. 잘못된 힙 사용을 탐지하기 위해 : 데이터 오버런(overrun) 및 언더런(underrun) 을 탐지하기 위해 탐지용 바이트를 추가로 할당할 수 있다.
2. 효율을 향상시키기 위해 : 힙 단편화 등 실행환경에 맞게 커스터마이징
3. 동적 할당 메모리의 실제 사용에 관한 통계 정보를 수집하기 위해 : 로그 수집 등


1. 할당 및 해제 속력을 높이기 위해
기본 메모리 관리자의 공간 오버헤드를 줄이기 위해
2. 적당히 타협한 기본 할당자의 바이트 정렬 동작을 보장하기 위해 : double 시에는 8바이트 정렬을 보장해야 프로그램 성능이 좋다
3. 임의의 관계를 맺고 있는 객체들을 한 군데에 나란히 모아 놓기 위해
4. 그때그때 원하는 동작을 수행하기 위해

# operator new 구현시 관례

1. 관례적으로, operator new 함수는 메모리 할당을 반복해서 시도하는 무한 루프를 가져야 하고, 메모리 할당 요구를 만족시킬 수 없을 때 new 처리자를 호출해야 하며, 0바이트에 대한 대책도 있어야 한다. 클래스 전용 버전은 자신이 할당하기로 예정된 크기보다 더 큰(틀린) 메모리 블록에 대한 요구도 처리해야 한다.
2. operator delete 함수는 널 포인터가 들어왔을 때 아무 일도 하지 않아야 한다. 클래스 전용 버전의 경우에는 예정 크기보다 더 큰 블록을 처리해야 한다.

```cpp
// 커스텀 operator new 함수는 다른 매개변수를 추가로 가질 수 있다
void* operator new(std::size_t size) throw(std::bad_alloc)
{
	using namespace std;

	// 0 바이트 요청이 들어오면 1 바이트 요구로 간주하고 처리
	if (size == 0)
	{
		size = 1;
	}
    
    // 메모리 할당 실패 시 루프를 통해 다시 시도
	while (true)
	{
		size 바이트를 할당해 본다;
		if (할당에 성공)
		{
			return (할당된 메모리에 대한 포인터);
		}

		// 할당에 실패했을 경우, 현재의 new 처리자 함수가
		// 어느 것으로 설정되어 있는지 찾아낸다
		new_handler globalHandler = set_new_handler(0);
		set_new_handler(globalHandler);

		if (globalHandler) (*globalHandler)();
		else throw std::bad_alloc();
	}
}
```
항목 49에서, new 처리자 함수는 가용 메모리를 늘려 주던가, 다른 new 처리자를 설치하든가, new 처리자의 설치를 제거하든가, bad_alloc 혹은 bad_alloc 에서 파생된 타입의 예외를 던지든가, 아예 함수 복귀를 포기하고 중단을 시켜야 한다고 이야기했다. 이렇듯 new 처리자 함수가 4가지 중 하나를 택해야 하는 이유는, 위의 operator new 함수의 구현처럼 내부 루프를 끝낼 수 있도록 구현되어야 하기 때문이다.

# operator new 상속된 경우 사이즈가 다를 수 있다.

상속받은 것은 base가 불림 (크기가 다르면 전역 operator new?)

```cpp
class Base
{
public:
		static void* operator new(std::size_t size) throw(std::bad_alloc)
	{
		// 0 바이트도 점검된다. 왜냐하면 Base 크기는 무조건 0 이상으로 잡히기 때문
		if (size != sizeof(Base))
			return ::operator new(size);

		...
	}
};
class Derived: public Base {};

int main()
{
	// Base::operator new 가 호출!
	Derived *p = new Derived;	
}



```
operator new[ ] 의 경우, 객체의 갯수를 (요구된 바이트 수 / sizeof(Base)) 로 계산할 수 없고(파생 클래스에서 사용할 수 있으므로) 인자로 넘어가는 size_t 타입의 인자는 객체들을 담기에 딱 맞는 메모리 양보다 더 많게 설정되어 있을 수도 있다. 이런 어려움 때문에, operator new[ ] 구현은 조금 더 까다롭다.


참고로, 가상 소멸자가 없는 기본 클래스로부터 파생된 클래스의 객체를 삭제하려고 하면 operator delete 로 C++ 가 넘기는 size_t 값이 엉터리일 수 있다!
 

# new는 최소 1byte는 무조건 할당해야 함.


# delete 두번 금지
delete p; // p가 NULL이어도 안전
T* p = new T; delete p; // (O) delete p; // (X) 두번 죽일 순 없다.



# 위치 지정 생성((Placement New))

T* p = new(buf) T; // 명시적 delete 호출

위치 지정 new, 명시적 소멸자 호출(재정의 방법은 EC++ 8장 참고)

void * operator new(size_t, void* p) {return p}; void* buf = malloc(sizeof(X)); // X 클래스만큼 할당
X* p = new(buf)X; // buf에 X 객체를 만든다. p->~X(); // 명시적 소멸자 호출
free(buf); // 할당된 

1. operator new 함수의 위치지정(placement) 버전을 만들 때는, 이 함수와 짝을 이루는 위치지정 버전의 operator delete 함수도 꼭 만들자. 이 일을 빼먹으면, 찾아내기도 힘들고 생겼다가 안 생겼다 하는 메모리 누출 현상을 경험하게 된다.
2. new 및 delete 의 위치지정 버전을 선언할 때는, 의도한 바도 아닌데 이들의 표준 버전이 가려지는 일이 생기지 않도록 주의하자.

```cpp
// 기본형 operator new
void* operator new(std::size_t size) throw(std::bad_alloc);

// 전역 유효범위에서의 기본형 시그니처
void operator delete(void* rawMemory) throw();
// 클래스 유효범위에서의 기본형 시그니처
void operator delete(void* rawMemory, std::size_t size) throw();
```


# nothrow new

예외불가(nothrow) new 는 영향력이 제한되어 있다. 메모리 할당 자체에만 적용되기 때문이다. 이후에 호출되는 생성자에서는 얼마든지 예외를 던질 수 있다.

new 는 bad_alloc 예외를 던짐
T* p = new T;
if (!p) {} // 예외를 던진다는데 왜 널검사를 하니? 널검사 하고 싶으면 다음처럼... T* p = new (std::nothrow) T; // 실패하면 0 리턴


C++ 표준 라이브러리의 함수는 new C++98 이후 C++ 표준에 지정된 동작을 지원합니다. 할당 요청에 operator new 대한 메모리가 부족한 경우 는 예외를 std::bad_alloc throw합니다.

이전 C++ 코드는 실패한 할당에 대해 null 포인터를 반환했습니다. throw하지 않는 버전 new이 필요한 코드가 있는 경우 프로그램을 와 nothrownew.obj연결합니다. 이 파일은 nothrownew.obj 할당이 실패할 경우 를 반환 nullptr 하는 버전으로 전역 operator new 을 대체합니다. operator new 더 이상 을 throw하지 않습니다 std::bad_alloc. 및 기타 링커 옵션 파일에 대한 nothrownew.obj 자세한 내용은 링크 옵션을 참조하세요.

동일한 애플리케이션에서 null 포인터를 확인하는 코드와 전역 operator new 예외를 확인하는 코드를 혼합할 수 없습니다. 그러나 다르게 동작하는 클래스 로컬 operator new 을 만들 수 있습니다. 이 가능성은 컴파일러가 기본적으로 방어적으로 작동하고 호출에 new null 포인터 반환에 대한 검사를 포함해야 한다는 것을 의미합니다. 이러한 컴파일러 검사를 최적화하는 방법에 대한 자세한 내용은 를 참조하세요 /Zc:throwingnew.

#  할당실패 테스트

```cpp
#include <iostream>
#include <new>
using namespace std;
#define BIG_NUMBER 10000000000LL
int main() {
   int *pI = new(nothrow) int[BIG_NUMBER];
   if ( pI == nullptr ) {
      cout << "Insufficient memory" << endl;
      return -1;
   }
}
```

# set_new_handler

set_new_handler 함수를 쓰면 메모리 할당 요청이 만족되지 못했을 때 호출되는 함수를 지정할 수 있다.



```cpp
namespace std
{
  typedef void (*new_handler)();

  // throw() 는 예외지정으로, 예외를 던지지 않는다
  new_handler set_new_handler(new_handler p) throw();
}

void outOfMem()
{
  std::cerr << "Unable to satisfy request for memory\n";
  std::abort();
}

int main()
{
  std::set_new_handler(outOfMem);

  // Unable to satisfy request for memory 출력!
  int *pBigDataArray = new int[100000000000000l];
}
```

operator new 와의 고려사항

1. 사용할 수 있는 메모리를 더 많이 확보한다
2. 다른 new 처리자를 설치한다 : 현재의 new 처리자 안에서 set_new_handler 를 설치하고 호출한다
3. new 처리자의 설치를 제거한다 : set_new_handler 에 널 포인터를 넘긴다
4. 예외를 던진다
5. 복귀하지 않는다 : 대개 abort 혹은 exit 을 호출한다

  1) 사용할 수 있는 메모리를 더 많이 확보한다.

    - 프로그램이 시작할때, 메모리 블록을 크게 할당해 놓았다가 new처리자 첫 호출(메모리할당 첫실패시) 될 때 그 메모리를 쓸수있도록한다.

  2) 다른 new 처리자를 설치한다.

    - 호출된 현재의 new처리자가 감당하지 못할때, 다른 new처리자를 설치한다.(현재의 new 처리자에서 set_new_handler(다른 new_handler)를 호출)

  3) new 처리자의 설치를 제거한다.

    - set_new_handler에 널 포인터를 넘긴다. new처리자 미설치시, operator new는 처음얘기한것처럼 사용자에러처리함수 없을시, 예외를 던진다.

  4) 예외를 던진다.

    - bad_alloc 혹은 bad _alloc에서 파생된 타입의 예외를 던진다. operator_new는 이 쪽종류의 에러 대응 능력이 없기에, 이 예외는 메모리 할당을 호출한쪽으로 예외 전이(propagate)된다.

  5) 복귀하지 않는다.

    - abort, exit 호출하여 종료시킨다.

```cpp
class NewHandlerHolder
{
public:
  explicit NewHandlerHolder(std::new_handler nh)
  : handler(nh) {}

  ~NewHandlerHolder()
  { std::set_new_handler(handler); }

private:
  // 핸들러를 기억해 둔다
  std::new_handler handler;

  // 복사를 막기 위한 부분 (항목 14 참고)
  NewHandlerHolder(const NewHandlerHolder&);
  NewHandlerHolder& operator=(const NewHandlerHolder&);
};
class Widget
{
public:
  static std::new_handler set_new_handler(std::new_handler p) throw(){
      std::new_handler oldHandler = currentHandler;
      currentHandler = p;
      return oldHandler;
  }
  static void* operator new(std::size_t size) throw(std::bad_alloc) {
    // Widget 의 new 처리자 설치
    // NewHandlerHolder 는 currentHandler 로 핸들러가 
    // 바뀌기 전의 oldHandler 를 쥐고 있음!
    NewHandlerHolder h(std::set_new_handler(currentHandler));

    // 할당 실패 시 예외를 던짐
    return ::operator new(size);

    // 이전의 전역 new 처리자가 자동으로 복원됨
  }
private:
  static std::new_handler currentHandler;
};

// 널로 초기화
std::new_handler Widget::currentHandler = 0;

int main()
{
  // Widget 객체에 대한 메모리 할당이 실패했을 때 호출할 함수
  void outOfMem();

  // Widget 의 new 처리자 함수로 outOfMem 설치
  Widget::set_new_handler(outOfMem);

  // 메모리 할당 실패 시 outOfMem 호출
  Widget *pw1 = new Widget;

  // 메모리 할당 실패 시 전역 new 처리자 함수 호출(있으면)
  std::string *ps = new std::string;

  // Widget 클래스의 new 처리자 함수를 null 로 설정
  Widget::set_new_handler(0);

  // 메모리 할당이 실패하면 이제 바로 예외를 던짐
  Widget *pw2 = new Widget;
}
```
