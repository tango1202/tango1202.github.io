---
layout: single
title: "#2. 메시지 전달"
categories: "win"
tag: ["Windows", "message", "message loop", "cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

메시지 처리를 위한 전형적인 메시지 루프는 다음과 같다.

```cpp
while (GetMessage(&msg, NULL, 0, 0)) { // WM_QUIT 까지 루프 실행
    TranslateMessage(&msg);  // 메시지 번역, WM_KEYDOWN이 WM_CHAR로...          
    DispatchMessage(&msg);  // 윈도우 프로시저 실행
}
```
`GetMessage()` 함수는 메시지 큐에 메시지가 있으면 메시지를 꺼내 오고 그렇지 않으면 대기한다. 꺼내온 메시지는 `TranslateMessage()` 를 통해 적절한 메시지로 번역되고, `DispatchMessage()`에 의해 윈도우 프로시저로 전달된다.

 

윈도우즈 시스템은 윈도우 프로시저에 메시지를 전달하는데 있어서 다음의 두가지 방식을 사용한다.

1. Queued Message : `WM_QUIT`, `WM_PAINT`, `WM_TIMER` 로서 메시지 큐에 메시지들이 적재되어 있다가 적절한 시점에 처리된다. 특히 `WM_PAINT`는 동일한 윈도우에 대하여 하나의 메시지로 통합되고, 메시지 큐가 비워졌을때 처리된다.(이때 여러 `Invalid` 영역은 하나로 합쳐질 것이다.) 이 때문에 화면 갱신의 횟수가 줄어든다. 

2. NonQueued Message : 기타 다른 메시지

메세지 큐는 Thread 생성시 자동으로 만들어 진다.

----------------------------------------------------

1. `TranslateAccelerator()`로 메시지 루프에서 단축키를 선처리할 수 있다.
 
    ```cpp
    hAccelTable = LoadAccelerators(hInstance, (LPCTSTR)IDC_MY_ACCEL);

    while (GetMessage(&msg, NULL, 0, 0)) {
        if (!TranslateAccelerator(msg.hwnd, hAccelTable, &msg)) {
            TranslateMessage(&msg);
            DispatchMessage(&msg);
        }
    }
    ```
 
2. `PeekMessage()`로 메시지 큐의 내용을 검사할 수 있다.

3. `GetMessageTime()`으로 메시지가 발생한 시간을 알 수 있다.

4. `GetMessagePos()`로 메시지가 발생했을 때의 커서 위치를 알 수 있다.

5. `WaitMessage()`로 메시지가 발생할 때까지 대기할 수 있다.

6. `PostMessage()`, `PostThreadMessge()`로 메시지 큐에 메시지를 추가 할 수 있다. 이때 메시지가 어떻게 처리되었는지의 응답을 기다리지 않는다.  이함수로 메시지를 큐에 넣고, 메시지 루프에 해당 메시지를 처리하는 루틴을 작성한다면, 윈도우 없는 어플리케이션을 작성할 수도 있다.

7. `PostQuitMessage()`로 `WM_QUIT`를 메시지 큐에 넣을 수 있다.

8. `SetMessageExtraInfo()`, `GetMessageExtraInfo()`로 특정 메시지에 대한 부가 정보를 사용할 수도 있다.

9. `SendMessage()`로 윈도우 프로시저에 직접 메시지를 전송할 수 있다. 이때 메시지가 10떻게 처리되었는지 응답을 기다린다.

10. `SendMessageTimeOut()`으로 메시지의 응답을 주어진 시간만큼 기다리게 할 수 있다.

11. `SendNotifyMessage()`로 메시지의 응답을 기다리지 않게 할 수 있다.

12. `InSendMessage()`, `ReplyMessage()`로 메시지 처리에 대한 응답을 빨리 해줄 수 있다.

    ```cpp
    case WM_USER + 1:
        if (InSendMessage()) {
            ReplyMessage(TRUE); // SendMessage()가 기다리지 않게 일단 빨리 응답
        }
        //.... 시간이 좀 걸리는 작업....
    break;
    ```
 

`Send` 계열의 메시지 전송 함수는 윈도우 프로시저에 직접 메시지를 전송하고, `Post` 계열의 메시지 전송 함수는 메시지 큐에 메시지를 추가한다는 사실을 유의할 것. `Post` 계열은 메시지 큐가 꽉 차면, 실패할 수 있다.