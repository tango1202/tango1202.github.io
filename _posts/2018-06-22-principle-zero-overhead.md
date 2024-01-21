---
layout: single
title: "#22. [개발 원칙] 제로 오버헤드 원칙(Zero Overhead Principle)"
categories: "principle"
tag: ["개발 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

[제로 오버헤드 원칙](https://tango1202.github.io/principle/principle-zero-overhead/)은 ***사용하지 않는 것에 비용을 지불하지 말라*** 는 원칙입니다.

다음의 경우가 [제로 오버헤드 원칙](https://tango1202.github.io/principle/principle-zero-overhead/)을 위반한 것이라 할 수 있습니다.

1. 미리 정의하고 생성하였으나 아직 사용되지 않는 변수

    다음의 `c`는 `if()`조건에 따라 사용되지 않을 수 있습니다.

    ```cpp
    void f() {
        int a;
        int b;
        int c; // 미리 정의해 뒀습니다.

        if (a + b == 0) {
            return 0; // (△) 비권장. int c 를 정의했지만 사용하지 않습니다.
        }

        return a + b + c;
    }
    ```

    사용할때 정의하시고, 이미지와 같은 경우는 [Proxy](https://tango1202.github.io/pattern/pattern-proxy/)를 이용하여 지연 생성하세요.

2. [전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)

    [전역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A0%84%EC%97%AD-%EB%B3%80%EC%88%98)는 시스템의 시작과 끝까지 메모리를 사용합니다. 정말 그럴 필요가 있는 변수라면 어쩔 수 없지만, 정말 필요한 것인지, 코딩 실수인지 꼭 확인하세요.

3. 언젠간 유용할 것 같지만 여전히 유용하지 않은 [쓰레기 코드](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%93%B0%EB%A0%88%EA%B8%B0-%EC%BD%94%EB%93%9Cgarbage-code)(*[필요할때 구현하라](https://tango1202.github.io/principle/principle-you-arent-gonna-need-it/) 참고*)

    * [인터넷에서 찾아서 추가](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%9D%B8%ED%84%B0%EB%84%B7%EC%97%90%EC%84%9C-%EC%B0%BE%EC%9D%8Cfound-on-internet) 하였으나 필요한 것보다 많이 복사된 코드
    * [캘린더 코더](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%BD%94%EB%94%A9-%EA%B4%80%ED%96%89-%EC%BA%98%EB%A6%B0%EB%8D%94-%EC%BD%94%EB%8D%94calendar-coder)가 맹목적으로 추가한 코드
    * [과잉 설계된 디자인](https://tango1202.github.io/principle/principle-anti-pattern/#%EB%82%98%EC%81%9C-%EC%84%A4%EA%B3%84-%EA%B4%80%ED%96%89-%EC%95%9E%EB%8B%A8%EC%9D%98-%ED%81%B0-%EB%94%94%EC%9E%90%EC%9D%B8big-design-up-front)으로 추가된 코드

사용하지 않는 것에는 비용을 지불하지 마세요. 컴파일하는 시간도 아깝고, 검색할때 괜히 검색되서 눈만 어지럽힙니다.