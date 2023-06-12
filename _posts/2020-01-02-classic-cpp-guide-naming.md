---
layout: single
title: "#1. [고전 C++ 가이드] 이름 짓기"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

C++ 은 C언어와 철학과 개념들이 많이 달라서, 이름 짓는 규칙도 좀 다릅니다. 선호하시는 규칙들이 있겠으나, 하기는 꼭 지키는게 좋습니다.

1. `_` 선택적 금지
   
    언더바는 표준에서 예약을 해버렸습니다.하기 규칙을 따르시는게 좋습니다.

    * 언더바로 시작하는 이름 금지(`_data`, X)
    * 이름내에 언더바가 두개 연속 금지(`my__data`, X)

2. 타입 명시 금지
    
    C언어에서 사용하던 헝가리안은 C++의 타입 일반화 철학과 맞지 않습니다. 개체 지향에 아무런 이점도 없고, 유지보수만 힘듭니다.

    * 헝가리안 금지(`int nCount`, X)

3. 매크로 대문자
   
    매크로는 대문자로 쓰지만, 아예 매크로 자체를 사용 안하는게 좋습니다.

    `const` 형 상수, `enum`, 인라인 함수로 매크로를 대치할 수 있습니다.

4. 반복되는 이름 

    ```cpp
    TextEditor textEditor;
    textEditor.SetTextSelectionPos(0, 3);
    textEditor.RemoveTextSelection();
    ```

    보다는

    ```cpp
    TextEditor textEditor;
    textEditor.Select(0, 3);
    textEditor.Remove();
    ```

    가 보기 좋습니다.