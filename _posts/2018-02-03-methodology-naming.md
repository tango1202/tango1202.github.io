---
layout: single
title: "#3. 작명하라"
categories: "methodology"
tag: ["개발방법론"]
author_profile: false
sidebar: 
    nav: "docs"
---

프로그래밍시 작명의 기술은 너무나도 중요합니다. [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability) 향상을 통해 생산성을 극대화해주고, 동료를 배려하는 아트 코딩의 시작점입니다. 좋은 이름을 짓는다면, 코드를 읽은 동료들이 당신의 고민과 배려에 감사해 할거에요.

# 불필요한 규칙

혹자는 이름이 길어지는게 싫어서 약어를 쓰기도 하고, 또 어떤 사람은 접두어나 접미어로 아키텍처적인 구분도 하기도 하죠.

1. 약어 혹은 미련할 정도의 약어 금지 규칙

    약어는 안쓰는게 좋습니다.

    `Horizontal`을 약어로 쓰는 방법은 모음 빼기 방법을 이용해 `Hrzntl` 이라고 쓰는 방법이 있고요, 어휘상 앞단어인 `Horiz` 를 쓰는 방법이 있고요, 흔히 쌍으로 같이 쓰이는 `Vertical` 의 `Vert` 와 단어 갯수를 일치 시키기 위해 `Horz` 라고 쓰는 방법이 있고요, 간단히 `H` 라고 쓰는 방법이 있습니다. 이렇게 여러가지 방법이 있는데, 통일이 될까요? 약어를 쓰다보면 동일한 프로젝트에서 서로 다른 표기법을 사용하여 혼란스러워 집니다. 이름 규칙의 혼란은 곧 코드의 혼란을 가져옵니다.

    그래요. 그래서 우리팀은 절대 약어는 안쓴다 라고 결심했습니다. 잘 지켜질까요? 당신의 의지가 아무리 강하더라도 시간이 지나다보면 느껴질 겁니다. 특히 변수명이 길어질수록, 단어가 복잡할수록 괜한 고집이었구나... 라는 생각이 들겁니다.

    `synchronization` 보단 `sync`가 오타의 소지가 적고 [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)은 오히려 더 좋습니다.

    그럼 어떻게 할까요? 약어는 안쓰는게 좋은데, 미련히 약어 금지를 고집해야 할까요?

    전, 적당한 선에서 타협을 해야 한다고 생각합니다. 제가 사용하는 원칙은 다음과 같습니다.

    * [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)이 향상되는 경우에만 약어를 씁니다.

    * [유효 범위](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-scope/)가 비교적 짧다면 약어를 씁니다. 함수의 [지역 변수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-static-extern-lifetime/#%EC%A7%80%EC%97%AD-%EB%B3%80%EC%88%98)나 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)로 사용되는 것인데, 함수의 라인수가 길지 않다면 써도 무방하다고 봅니다.
   
    * 검색하기 힘들거나 검색 정확도를 떨어뜨리는 약어는 쓰지 않습니다.

        `Hrzntl`, `Horz`는 원래 단어가 `Horizontal` 입니다. 검색창에 `Hrzntl`, `Horz`를 아무리 입력해도 원래 단어인 `Horizontal`은 검색되지 않으니 사용하지 않습니다. 이는 소스코드에서 변수의 의미, 영향도를 파악하기 위해 검색을 사용할때 치명적일 수 있으므로 꼭 지켜져야 합니다.

        또한 `Horizontal` 을 `h` 로 쓰고, `Height`도 `h` 로 썼다면, `h` 로 검색했을때 구별을 할 수 없습니다. 하지만 다음과 같이 검색범위가 비교적 적거나 검색시 의미가 보존될 수 있다면 사용하기도 합니다.

        ```cpp
        void SetSize(int w, int h) {
            m_Width = w; // 약어인 w를 사용하더라도 width 검색시 검색됩니다. 코드상으로도 w의 의미를 직관적으로 알 수 있습니다.
            m_Height = h;
        }
        ```
 
 2. 언어 특징에 따른 접두사

    흔히 헝가리안 표기법이라고 해서 C 언어에서 Windows 프로그래밍을 할때 많이 사용되는 기법이 있었습니다. 안타깝게도 MFC나 Windows API 에 종종 등장하여 이게 표준적인 것이구나. 따라해야지... 라는 생각을 갖게 되나 봅니다.(*저도 초창기엔 열심히 따라썼습니다.*) 근데요, 시간이 지날 수록 장점보다는 단점이 많다는걸 깨달았습니다.

    결론을 말하면 적어도 개체 지향 프로그래밍에서는 "타입에 의존적인 헝가리안 표기법"은 사용 금지 입니다.

     `int nCount;` 요런건, 걍 `int count;` 로 하는게 좋다는 뜻입니다.

    이유는요, `int`가 `long` 으로  바뀔 수도 있고요, `unsigned int` 로 바뀔 수도 있고요, `Count` 클래스 타입으로 될 수도 있기 때문입니다. 이때마다 이름을 바꿔야 하는데, 사실 이런 이름 변경에 대한 유지보수까지 신경 쓰기에는 항상 개발 일정은 촉박하죠. 더군다나 C++는 template을 통한 [일반화 프로그래밍](https://tango1202.github.io/legacy-cpp-stl/legacy-cpp-stl-generic/)으로, 타입에 대해 독립적인 기능을 만들기 위해 언어적 차원에서 지원까지 하는데, 굳이 변수명 앞에 타입에 대한 접두어를 붙여가며, 타입 종속적으로 만들 필요가 없습니다.

    코드의 유지보수, 타입에 대한 독립성 보장을 위해 헝가리안 표기법은 사라져야 하는게 맞습니다.

    누가 그러더군요. 코드를 짜다보면 `count` 가 `int` 형 변수인 걸 알면서 짜면 큰 도움이 된다구요. 그래서 `n`을 붙여야 한다는 군요. 이는 다음과 같은 이유에서 잘못되었습니다.

    * `int count;` 라고 선언하여 `int` 형인걸 이미 알고 있습니다. 그런데도 굳이 `nCount` 라고 사용해야 된다는 건, 변수 선언부와 해당 변수를 사용하는 곳이 아주 아주 멀리 떨어져 있다는 의미입니다. 선언부와 사용부가 멀리 떨어져 있는 코드는 리팩토링 되어야 합니다. 선언부와 사용부를 최대한 가까이 작성하시기 바랍니다. 그게 개체 지향 프로그래밍입니다.

    * `nCount` 라고 쓰면 본인도 모르게 `count` 가 `int` 형이라고 가정하고 작성하게 됩니다. 하지만 이러한 가정은 언제든지 바뀔 수 있고요, 가정이 바뀌면 당신의 코드는 어떤 잉여효과를 쏟아낼지 예측하기 힘듭니다. 되도록이면 가정하지 마세요. 만약 가정한다면 `assert`도 도배해 두고요.

    또, 클래스 앞에 C 를 붙이거나 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/) 앞에 `e`를 붙이거나, [매크로 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98)는 대문자로 쓰거나 하는 일반적인 C++ 언어의 관습도 사라져야 합니다. C 접두어를 써가며 이게 굳이 "클래스다"라고 인지하며 코딩하는 건 좋지 않은 습관입니다. 모든 가정을 버리셔야 좋은 코드가 작성됩니다. 사실 클래스라고해서 다르게 처리하지도 않잖아요?

    [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)도 마찬가지입니다. 일반적으로 코딩할때 `e` 접두어가 없으면 낯설어하시는 분들이 많습니다. 이런분들은 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)을 전역적으로 정의하고 사용하시는데 익숙하신 분들입니다. 클래스 내부에 국지적으로 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)을 정의하신다면, 이름 충돌의 우려도 적을 뿐 아니라 이름도 간결해 집니다.

    ```cpp
    class File {
    public:
        enum Error {
            Ok,
            Fail,
            CannotFind
        };
        Error Load(const String& filename);
    };
    ```

    위의 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)을 전역적으로 사용하여,

    
    ```cpp
    enum FileError {
        eFileErrorOk,
        eFileErrorFail,
        eFileErrorCannotFind
    };
    ```
 
    로 하는게 더 좋은 분 계신가요? 없죠?

    손가락 타이핑 수도 줄이고 코드도 간결한 클래스 내부 [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)을 쓰시기 바랍니다.

    > *(C++11~)  [범위 있는 열거형](https://tango1202.github.io/cpp/modern-cpp-scoped-enum/)이 추가되어  이름 충돌 회피가 쉬워졌고, [암시적 형변환](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EC%95%94%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)을 차단하며, [전방 선언](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-include/#%EC%A0%84%EB%B0%A9-%EC%84%A0%EC%96%B8)도 지원합니다. [범위 있는 열거형](https://tango1202.github.io/cpp/modern-cpp-scoped-enum/)을 사용하는게 더 좋습니다.*

    그리고, [매크로 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98) 대문자는 이제 불필요하죠. 맞죠? [매크로 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98) 쓰지 마세요. [열거형](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-enum/)을 쓰시기 바랍니다. [매크로 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98)는 개체 지향 프로그래밍의 사상과 철학을 해치는 "goto 문 보다 더 나쁜 코딩 습관" 입니다.

    그래도 팀 누군가가 [매크로 상수](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-preprocessor/#%EB%A7%A4%ED%81%AC%EB%A1%9C-%EC%83%81%EC%88%98)를 자꾸 쓴다고 화가 나서, 헤더 파일 아무곳에나 다음과 같이 작성하진 마시고요. 

    ```cpp
    #define public private
    ```

    빌드 오류 잡느라 한 2~3일 고생하게 됩니다.

3. 아키텍처 레이어에 따른 접두사

    중대형 프로젝트의 경우 아키텍처에 따라 레이어로 나누어 코딩하게 됩니다.(*[관심사의 분리 원칙](https://tango1202.github.io/principle/principle-separation-of-concerns/) 참고*) 이때 클래스가 소속한 레이어를 구분하기 위해 각 구현 클래스 마다 레이어 명을 접두어로 붙이는 경우가 있습니다.

    초기에는 좋아보입니다. 이 클래스가 어느 레이어에 포함되었는지 클래스 명만 봐도 알거 같네요. 대략 이 클래스를 사용할때 어느 레이어에 있으니까 어떤걸 주의해야 할지 알것도 같습니다. 약간의 손 타이핑은 오히려 타자 속도를 더 올려주는 듯도 하고, 괜히 열심히 일하는 듯한 느낌도 들게 하네요.

    하지만, 아키텍처는 사용자 요구사항, 시스템의 발전에 따라 재구성되거나 재배치되어야 합니다. 아키텍처 레이어에 따른 접두사 때문에 리팩토링은 더 힘들어지고요, 여러분의 소프트웨어는 더디게 발전할 겁니다. 더군다나 이전에 레이어가 포함된 이름이 이미 몸에서 익숙해졌다면, 새로 구성된 아키텍처가 이상하게 느껴지겠네요. 손가락 관절염은 더 심해지고요.

    특히나 회사명이나 제품명, 코드명을 접두어로 넣지 마세요.

    회사명, 제품명 바뀐다고 소스코드 바꾸지도 않을꺼고요, 코드명을 소스에 붙여대는 건 정말이지 아무짝에도 쓸모없는 짓이니까요. 버전이 업그레이드 되어 코드명은 바꿨지만, 여전히 당신의 소스코드는 1.0 때의 코드명이 도배가 되어 있을 겁니다. 이런거 바꾸기엔 우린 할일이 너무 많잖아요?

    C++는 [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)라는 좋은 기능이 있습니다. 아키텍처 레이어링하는데 탁월한 효과를 보여줍니다. 몰라서 못썼다면 공부해 보시고요, 아는데도 안썼다면, 한번 시도해 보시기 바랍니다.

# 좋은 규칙

이름은 구체적이면서 직관적인게 좋습니다.

예를 들어 예전 프로젝트에 `OnNotifyTask` 라는 이벤트 함수명이 있었습니다.

```cpp
protected:
    // 상속받은 곳에서 재구현
    virtual void OnNotifyTask();
```
 
여러분은 뭐가 궁금한가요? 전 이 이벤트가 도대체 언제 발생하는지 궁금했고, 이 이벤트가 발생하면 무슨 행동을 취해야 하는지 궁금했습니다.

더욱 놀라운건,

1년전에 작성된 함수였고 3명의 개발자가 유지보수한 코드에서 발견되었음에도, 3명 모두가 위의 질문에 대한 정확한 해답을 제시하지 못했다는 거였습니다.

기억나지 않습니다... 라는 대답만 들었죠.

결국 장기간의 분석과 논의를 거쳐 이벤트 함수의 이름이 다음과 같이 바뀌었습니다.
 
```cpp
protected:
    // 개체 선택이 변경되거나 취소될 때 발생.(동일 개체 재선택시에는 발생하지 않음)
    virtual void SelectChanged();
```

`protected:` 와 `virtual`이 상속받아 재구현 하라는 의미로 전달되므로, "상속받은 곳에서 재구현" 이라는 주석은 뺐고요, 이벤트라는 의미의 `On`도 빼버렸습니다. 선택이 내부적으로 완료된 후 발생하는 이벤트라는 의미로 `Change`나 `Changing` 이 아니라 `Changed`를 사용했습니다.

좋은 이름은 생각보다 쉽게 만들 수 있습니다. 그냥 조직원과 함께 만들면 됩니다.(*[짝 프로그래밍](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%A7%9D-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8Dpair-programming), [코드 리뷰](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EB%A6%AC%EB%B7%B0code-review) 참고*) 누군가가 이게 뭐냐고 물어보거나, 설명을 위해 주석이 필요하다면, 나쁜 이름입니다. 

다음을 지키세요.

1. 일관성을 유지하세요.

    * 유사한 이름을 여러개 사용하지 마세요. `remove`, `delete`, `erase`는 모두 삭제와 관련된 것입니다. 하나만 선택하시길 바랍니다.
    * 구조에 맞는 일관된 이름을 사용하세요. 예를 들어 `XmlWriter`, `JsonWriter`는 `IWriter`를 상속한 개체라는 일관된 느낌을 줍니다.
    * 반대되는 개념의 단어쌍을 사용하세요. `Get`-`Set`, `Attach`-`Detach`은 유명한 단어쌍입니다.
    * `CRUD`는 `Create-Read-Update-Delete`입니다. 생성-읽기-갱신-삭제에 일관성을 주세요.
    * `Proxy`, `Adapter`등 디자인 패턴에서 제시한 공통된 이름을 사용하세요.
    * 모든 팀원이 동일하게 작명할 수 있도록 [코딩 컨벤션](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%94%A9-%EC%BB%A8%EB%B2%A4%EC%85%98coding-conventions)에 규칙을 포함하세요. 

2. 코드의 의도를 전달하는 이름을 선택하세요.

    * `x`, `y`는 좌표의 의미이고, `width`, `height`는 너비와 높이입니다. `value`로 퉁치지 마세요.
    * 적절한 추상화 수준을 가져야 합니다.

3. 미련하게 반복하지 마세요.

    * `int SetWidth(int val)` 에서 `val`은 누가 뭐래도 너비일 겁니다. `int SetWidth(int width)`에서 `width`는 불필요한 코드 중복일 뿐입니다.
    
4. 오타 확률이 적은 단어를 선택하세요. 필요하면 약어도 괜찮습니다.

    * `initialization`, `synchronization` 보다는 `init`, `sync`로 축약하는게 오타 확률이 적습니다.
    * 모음제거법은 사용하지 마세요. `Horizontal`에서 모음을 제거하면 `Hrzntl`인데, 검색하기 어렵습니다. 오타 확률을 줄이려면 그냥 앞단어를 사용하여 `Horiz`가 좋습니다.
    * 헝가리안은 쓰지 않습니다.
    * 약어는 발음이 가능하고, 검색이 가능해야 합니다.

5. 함수는 동사로 시작하고, 개체는 명사나 형용사로 작성하세요.

잊지마세요. 다음이 바로 작명의 대 원칙입니다.

1. Kiss - Keep It Simple, Stupid

2. 작명은 [가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability)을 향상시키고 주석을 줄여주는 프로그래밍 기술입니다.(*[코드 가독성](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%BD%94%EB%93%9C-%EA%B0%80%EB%8F%85%EC%84%B1code-readability), [이름 짓기](https://tango1202.github.io/principle/principle-practices/#%EC%A2%8B%EC%9D%80-%EC%BD%94%EB%93%9C-%EC%9D%B4%EB%A6%84-%EC%A7%93%EA%B8%B0naming-things) 참고*)

3. 이름에 불필요한 장식을 하지 마세요. 손가락만 아프고, 나중에 바꾸느라 고생만 합니다.