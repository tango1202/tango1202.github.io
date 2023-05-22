---
layout: single
title: "#3. 메시지 루프"
categories: "win"
tag: ["Windows", "message", "message loop", "cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

일반적인 메시지 루프는 다음과 같다.
 
```cpp
MSG msg;
while (::GetMessage(&msg, NULL, NULL, NULL)) {
 ::TranslateMessage(&msg);
 ::DispatchMessage(&msg);
}
```
 

`GetMessage()`가 `FALSE`를 리턴하는 경우는 `WM_QUIT` 메시지를 발생하는 경우이므로 위 루프는 `WM_QUIT`를 발생할때까지 계속되며, 메시지 큐에 메시지가 추가되면 `TranslateMessage()`와 `DispatchMessage()` 함수를 실행한다. 메시지 큐에 메시지가 없다면, `GetMessage()`함수는 메시지가 추가될 때까지 CPU 점유없이 대기할 것이다.
`GetMessage()` 에서 에러가 발생된다면 -1을 리턴하므로 다음과 같이 에러처리 코드를 추가해 주는 것이 좋다.

```cpp
MSG msg;
while (1) {
    BOOL result = ::GetMessage(&msg, NULL, NULL, NULL);
    if (result == 0) {
        break; // WM_QUIT
    }
    else if (result == -1) {
         // error 발생. msg 포인터가 유효하지 않거나, hWnd가 유효하지 않은 윈도우 핸들인 경우
        DWORD error = GetLastError();                                            
    }
    else {
        ::TranslateMessage(&msg);
        ::DispatchMessage(&msg);
    }
}
```

이와 같은 메시지 처리를 다음과 같이 간략히 함수화 하여 작성할 수 있다.
 
```cpp
while (1) {
    if (!PumpMessage(&msg, NULL, NULL, NULL)) {
        break;// WM_QUIT
    }
}

BOOL PumpMessage(LPMSG lpMsg, HWND hWnd, UINT wMsgFilterMin, UINT wMsgFilterMax) {
    BOOL result = ::GetMessage(&msg, NULL, NULL, NULL);
    if (result == 0) {
        return FALSE; // WM_QUIT
    }
    else if (result == -1) {
        // error 발생. msg 포인터가 유효하지 않거나, hWnd가 유효하지 않은 윈도우 핸들인 경우
        DWORD error = GetLastError(); 
    }
    else {
        ::TranslateMessage(&msg);
        ::DispatchMessage(&msg);
    }
    return TRUE;
}
```
 
App 에서와 같은 주 쓰레드에서는 일반적으로 위와 같이 메시지 루프를 작성하지만, 그 이외의 곳에서 사용한다면 다음과 같이 `WM_QUIT`를 `post` 해줄 필요가 있다.
 
```cpp
while (1) {
    if (!PumpMessage(&msg, NULL, NULL, NULL)) {
        ::PostQuitMessage(0); // WM_QUIT 를 주 쓰레드로 전송
        break;
    }
}
```

처리시간이 꽤 긴 함수를 작성할 경우, 메시지 루프를 교묘히 사용하여 사용자의 체감속도를 줄일 수 있다. 아마도 다음 함수는 꽤 오랜 시간동안 실행될 것이다.
 
```cpp
void LongTimeMethod(){
    for (INT i = 0; i < 65535; i++) {
        Method(i);
    }
}
```

기다림에 익숙하지 않은 사람이라면, 함수의 실행이 끝나기도 전에 다운됐다고 생각하고 컴퓨터를 강제로 꺼버릴지도 모른다. 따라서 대부분의 프로그램은 현재 진행상황을 사용자에게 알려주길 원한다.
 
```cpp
void LongTimeMethod() {
    for (INT i = 0; i < 65535; i++) {
        Method(i);
        SetDlgItemInt(IDC_EDIT, i); // 진행상황을 표시. 그러나 잘 안된다.
    }
}
```
반복되는 루프안에 진행상황을 컨트롤에 표시하더라도 잘 안될 것이다. 이는 윈도우즈가 메시지 기반이기 때문이다. 위의 상황을 보다 세부적으로 생각해 보면 다음과 같이 해석할 수 있다.

1. `SetDlgItemInt()` : 컨트롤에 표시하기 위해 내부적으로 `SendMessage()`를 호출

2. `SendMessage()`에 의해 컨트롤의 윈도우 프로시저 호출

3. 컨트롤의 윈도우 프로시저가 컨트롤에서 표시할 값 세팅

4. 컨트롤의 윈도우 프로시저가 화면갱신을 위해 `Invalidate()` 호출

5. `WM_PAINT` 메시지가 메시지 큐에 삽입

6. 다시 `for()` 루프를 실행하여 1~5의 과정 반복

7. `for()` 루프가 종료하면, 메시지 큐에서 중복된 WM_PAINT 메시지를 병합

8. 컨트롤의 화면 갱신. 65534 가 표시됨

따라서, 진행상황은 표시되지 않고, 진행이 종료한 다음 그 결과만 표시될 것이다. 이를 해결하기 위해 다음과 같이 `UpdateWindow()`를 강제로 호출할 수 있다.
 
```cpp
void LongTimeMethod() {
    for (INT i = 0; i < 65535; i++) {
        Method(i);
        SetDlgItemInt(IDC_EDIT, i); // 진행상황을 표시. 그러나 잘 안된다.
        GetDlgItem(IDC_EDIT)->UpdateWindow(); // 즉시 화면 갱신
    }
}
```

`UpdateWindow()`는 즉시 화면을 갱신하므로, 진행상황이 화면에 표시된다. 하지만, 마우스, 키보드 입력등 사용자 입력에 따른 메시지 처리가 되지 않으므로 여전히 답답하다.

이러한 경우 메시지 루프를 사용하여 메시지 처리를 해주어야 한다.

```cpp
void LongTimeMethod() {
      for (INT i = 0; i<65535; i++) {
          // 메시지 처리. 그러나 운좋게 된다.
          if (!PumpMessage(&msg, NULL, NULL, NULL)) {
              ::PostQuitMessage(0); // WM_QUIT 를 주 쓰레드로 전송
              break;
          }
          Method(i); 
          SetDlgItemInt(IDC_EDIT, i); // 진행상황을 표시. 
    }
}
```
 

`PumpMessage()`는 내부적으로 `GetMessage()`를 사용하고 있는데, 이 함수는 메시지 큐에 메시지가 추가될때까지 대기한다. 위의 예에서는 `SetDlgItemInt()`에서 `WM_PAINT`를 지속적으로 메시지 큐에 넣으므로, `GetMessage()`가 대기하지 않으며, `Method()`는 65535 회 실행될 것이다.

하지만 일반적으로 위와 같은 경우에서는 `GetMessage()` 호출전에 `PeekMessage()`를 이용하여 메시지 큐에 메시지가 있는지 미리 검사하는 과정이 필요하다.

`PeekMessage()`는 `GetMessage()`와 달리 메시지 큐에 메시지가 없더라도 즉시 리턴된다. 따라서, 메시지 큐를 검사하는 용도로 사용할 수 있다.

```cpp
void LongTimeMethod() {
        for (INT i = 0; i<65535; i++) {
            // 메시지가 있는 경우만 GetMessage()
            if (::PeekMessage(&msg, NULL, NULL, NULL, PM_NOREMOVE)) {
                if (!PumpMessage(&msg, NULL, NULL, NULL)) {
                    ::PostQuitMessage(0); // WM_QUIT 를 주 쓰레드로 전송
                    break;
                }
            }
        Method(i); 
        SetDlgItemInt(IDC_EDIT, i); // 진행상황을 표시. 
    }
}
```
 
메시지가 없는 idle 시간을 활용하기 위해서는 일반적으로 다음과 같이 메시지 루프를 작성할 수 있다.

```cpp
while (1) {   
    // 메시지가 없다면 계속해서 OnIdle()을 호출한다.
    while (!::PeekMessage(pMsg, NULL, NULL, NULL, PM_NOREMOVE)) {
         OnIdle();
    }
    if (!PumpMessage(&msg, NULL, NULL, NULL)) {
        ::PostQuitMessage(0); // WM_QUIT 를 주 쓰레드로 전송
        break;
    }
}
```
 
위의 경우의 문제점은 CPU를 100% 가깝게 사용한다는 것이다. `PeekMessage()`가 즉시 리턴되고, `GetMessage()`는 메시지 큐에 메세지가 추가될때까지 대기한다는 사실을 상기하라! 메시지 큐에 메시지가 없는 동안에는 쉴새없이 OnIdle()을 호출할 것이다.
따라서, `OnIdle()`이 필요한 시기에 한번만 호출되도록 제어할 필요가 있다.
 
```cpp
BOOL idle = TRUE;
while (1) {
    // OnIdle 처리 블럭
    // OnIdle을 호출할 필요가 있고, 메시지가 없다면 OnIdle()을 호출한다.
    while (idle && !::PeekMessage(pMsg, NULL, NULL, NULL, PM_NOREMOVE)) {
        idle = OnIdle(); // OnIdle()이 FALSE를 리턴하면, 다음 메시지 발생시까지 OnIdle()이 발생하지 않도록 한다.
    }

    // 메시지 처리 블럭
    if (!PumpMessage(&msg, NULL, NULL, NULL)) {
        ::PostQuitMessage(0); // WM_QUIT 를 주 쓰레드로 전송
        break;
    }

    idle = TRUE; // 어떤 메시지가 처리되었다면 다음번에 OnIdle이 호출될 수 있게 한다.
}
```

`OnIdle()`에는 주로 현재 App의 상태에 따른 정보를 출력하며, 이벤트 방식의 윈도우즈 프로그램에서는 이벤트에 의해 App의 상태가 바뀌므로, 위의 `OnIdle()` 로직은 성공할 것이다. 적어도 새로운 이벤트가 발생된 다음에만 `OnIdle`이 호출된다.

`OnIdle()`이 처리되고 나면 `GetMessage()`가 호출되어 메시지 큐에 메시지가 추가되기를 기다리므로, CPU를 선점하지도 않는다.

위 코드의 약간의 문제가 있다면, `PumpMessage()`에서 `DispatchMessage()`를 호출했을때 어플리케이션 구현 상태에 따라 메시지 큐에 또다른 메시지가 추가되었을 수 있다는 것이다.
따라서, 다음과 같이 구현하여 메시지 큐의 모든 메시지를 처리한 후 `OnIdle()` 처리 블럭이 실행되도록 하는 것이 좋다.

```cpp
BOOL idle = TRUE;
while (1) {
    // OnIdle 처리 블럭
    // OnIdle을 호출할 필요가 있고, 메시지가 없다면 OnIdle()을 호출한다.
    while (idle && !::PeekMessage(pMsg, NULL, NULL, NULL, PM_NOREMOVE)) {
        idle = OnIdle(); // OnIdle()이 FALSE를 리턴하면, 다음 메시지 발생시까지 OnIdle()이 발생하지 않도록 한다.
    }
    // 메시지 처리 블럭 - 메시지 큐에 있는 모든 메시지를 처리한다.
    do {
        if (!PumpMessage(&msg, NULL, NULL, NULL)) {
            ::PostQuitMessage(0); // WM_QUIT 를 주 쓰레드로 전송
            goto ExitLoop; // 중첩된 루프 탈출
        }

        idle = TRUE; // 어떤 메시지가 처리되었다면 다음번에 OnIdle이 호출될 수 있게 한다.
    } while (::PeekMessage(&msg, NULL, NULL, NULL, PM_NOREMOVE));
}

ExitLoop:
```
 