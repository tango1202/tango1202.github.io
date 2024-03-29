---
layout: single
title: "#2. [레거시 C++ 가이드] 이름 짓기"
categories: "legacy-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * 밑줄로 시작하지 말고, 이중 밑줄을 사용하지 마라.
> * 헝가리안 표기법을 사용하지 마라.
> * [매크로](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/)는 대문자로 작성하라.
> * 이름을 반복하지 마라.

# 개요

이름 짓는 규칙에 있어 각자 선호하시는 규칙들이 있겠으나, 하기는 꼭 지키는게 좋습니다.

# _ 선택적 금지
   
밑줄(`_`)은 향후 확장을 위해 표준에서 하기의 예약을 해버렸습니다.

* 어디서나 이중 밑줄이 있는 식별자
* 밑줄과 대문자로 시작하는 식별자
* 글로벌 [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)에서 밑줄로 시작하는 식별자

그래서, 하기 규칙을 따르시는게 좋습니다.

* 밑줄로 시작하는 이름 금지(*`_data`, X*)
* 어디서든 이중 밑줄 금지(*`my__data`, X*)

# 타입 명시 금지
    
C언어에서 사용하던 헝가리안은 C++의 [타입 일반화 철학](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-generic/)(*어느 타입이던 동작하는 `swap()`등*)과 맞지 않습니다. 개체 지향에 아무런 이점도 없고, 유지보수만 힘듭니다.

* C언어 스타일의 헝가리안 표기법 금지(*`int nCount`, X*)

# 매크로 대문자
   
[매크로](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/)는 대문자로 씁니다.(*사실 아예 [매크로](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/) 자체를 안쓰는게 좋습니다. [매크로](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/)는 [상수 개체](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-const-mutable-volatile/#%EC%83%81%EC%88%98-%EA%B0%9C%EC%B2%B4), [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/), [인라인 함수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-inline/), [typedef](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-type/#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD) 로 대치할 수 있습니다.*)

# 반복하는 이름 

```cpp
TextEditor textEditor;
textEditor.SetTextSelectionPos(0, 3); // 이름이 길어요.
textEditor.RemoveTextSelection();
```

보다는

```cpp
TextEditor textEditor;
textEditor.Select(0, 3); // 이름이 짧고 간결합니다.
textEditor.Remove();
```

가 좋습니다. 

이름따위 반복되던 말던 컴파일만 되면 괜찮다고 하실 수도 있는데요, 반복되는 이름을 피하며 코딩하면, 자연스럽게 구조가 만들어지고 API가 설계됩니다. 

꼭 지키시기 바랍니다. ***좋은 코드는 좋은 이름에서부터 시작됩니다.***
