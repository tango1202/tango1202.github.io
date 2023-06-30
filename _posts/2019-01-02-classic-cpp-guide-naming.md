---
layout: single
title: "#2. [고전 C++ 가이드] 이름 짓기"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

C++ 은 C언어와 철학과 개념들이 많이 달라서, 이름 짓는 규칙도 좀 다릅니다. 선호하시는 규칙들이 있겠으나, 하기는 꼭 지키는게 좋습니다.

# `_` 선택적 금지
   
밑줄(`_`)은 향후 확장을 위해 표준에서 하기의 예약을 해버렸습니다.

* 어디서나 이중 밑줄이 있는 식별자
* 밑줄과 대문자로 시작하는 식별자
* 글로벌 네임스페이스에서 밑줄로 시작하는 식별자

그래서, 하기 규칙을 따르시는게 좋습니다.

* 밑줄로 시작하는 이름 금지(`_data`, X)
* 어디서든 이중 밑줄 금지(`my__data`, X)

# 타입 명시 금지
    
C언어에서 사용하던 헝가리안은 C++의 타입 일반화 철학과 맞지 않습니다. 개체 지향에 아무런 이점도 없고, 유지보수만 힘듭니다.

* 헝가리안 금지(`int nCount`, X)

# 매크로 대문자
   
매크로는 대문자로 쓰지만, 아예 매크로 자체를 사용 안하는게 좋습니다.

매크로는 `const` 변수, `enum`, 인라인 함수, `typedef` 로 대치할 수 있습니다.

# 반복되는 이름 

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

가 보기 좋습니다.
