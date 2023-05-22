---
layout: single
title: "#4. 메시지 루프 - 대화상자"
categories: "win"
tag: ["Windows", "message", "message loop", "cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

`Modeless` 대화상자를 지원하기 위해서는 대화상자에서 직접 처리하는 키보드 메시지(`TAB`, `ESC`, `ENTER`, `ALT+니모닉` 등)를 중복해서 처리하지 않도록 다음과 같이 메시지 루프를 사용해야 한다.

```cpp
BOOL PumpMessage(LPMSG lpMsg, HWND hWnd, UINT wMsgFilterMin, UINT wMsgFilterMax) {
    BOOL result = ::GetMessage(&msg, NULL, NULL, NULL);
    if (result == 0) {
        return FALSE; // WM_QUIT
    }
    else if (result == -1) {
        // error 발생. msg 포인터가 유효하지 않거나, hWnd가 유효하지 않은 윈도우 핸들인 경우
        DWORD error = GetLastError();
    }
    // 대화상자 메시지가 아니고, 단축키도 아니면 메시지 처리
    else if (!IsDialogMessage(hwndDlgModeless, &msg) &&
        !TranslateAccelerator(hwndMain, haccel, &msg)) {        
        ::TranslateMessage(&msg);
        ::DispatchMessage(&msg);
    }
    return TRUE;
}
```

일반적으로 `DialogBoxIndirect()`로 생성한 `Modal` 대화상자는 내부적으로 사용하는 메세지 루프에서 위의 과정을 처리해 주어 `IsDialogMessage()`를 따로 호출할 필요가 없다.

하지만, MFC에서는 `CDialog::PreTranslateMessage()`에서 `IsDialogMessage()`함수를 호출하여 `Modal` 대화상자이건, `Modeless` 대화상자이건 상관없이 대화상자 키보드 메시지를 무시하도록 되어있다.

이는 MFC 에서의 `Modal` 대화상자는 사실 `CreateDialogIndirect()`를 호출하여 생성된 `Modeless` 대화상자이며, 메시지 루프에 의해서 `Modal` 대화상자처럼 구현되어 있기 때문이다.

MFC 처럼 대화상자의 `Modal` 을 구현하기 위해서는 대화상자의 종료상태를 검색하는 과정이 필요하다.

다음은 대략적인 `DoModal`의 의사 구현 코드다.

```cpp
void Dialog::DoModal() {
    // MainFrame 아니면, 최상위 Parent Window
    HWND owner = GetOwner();
 
    // 대화상자 생성...
    CreateDialogIndirect();

    // Owner 비활성
    EnableWindow(owner, FALSE);

    while (1) {
        if (!PumpMessage(&msg, NULL, NULL, NULL)) {
            ::PostQuitMessage(0); // WM_QUIT 를 주 쓰레드로 전송
            break;
        }
        // EndDialog() 가 호출될 때 아마도 이 함수가 FALSE 가 리턴되도록 플래그 설정을 해야 한다.
        if (!IsContinue()) {  

            break;
        }
    }

    // Owner 활성
    EnableWindow(owner, FALSE);
    SetActiveWindow(owner, TRUE);

    // 대화상자 종료
    DestroyWindow();
}
```

대화상자의 종료는 버튼 클릭등의 이벤트에 의해서 처리되는 것이므로, `DispatchMessage()` 문 다음에 `IsContinue()` 를 배치하여 실행하더라도 무리가 없다.
`idle` 처리를 위해 다음과 같이 수정할 수도 있다.

 
```cpp
BOOL idle = TRUE;
while (1) {
    // OnIdle 처리 블럭
    // OnIdle을 호출할 필요가 있고, 메시지가 없다면 OnIdle()을 호출한다.
    while (idle && !::PeekMessage(pMsg, NULL, NULL, NULL, PM_NOREMOVE)) {
        // owner에 WM_ENTERIDLE 을 전송하여 idle 처리를 할 수 있게 한다.
        ::SendMessage(owner, WM_ENTERIDLE, MSGF_DIALOGBOX, (LPARAM)m_hWnd);

        idle = FALSE;
    }

    // 메시지 처리 블럭 - 메시지 큐에 있는 모든 메시지를 처리한다.
    do {
        if (!PumpMessage(&msg, NULL, NULL, NULL)) {
            ::PostQuitMessage(0); // WM_QUIT 를 주 쓰레드로 전송
            goto ExitLoop; // 중첩된 루프 탈출
        }

        // EndDialog() 가 호출될 때 아마도 이 함수가 FALSE 가 리턴되도록 플래그 설정을 해야 한다.
        if (!IsContinue()) {  

            goto ExitLoop; // 중첩된 루프 탈출
        }

        idle = TRUE; // 어떤 메시지가 처리되었다면 다음번에 OnIdle이 호출될 수 있게 한다.
    } while (::PeekMessage(&msg, NULL, NULL, NULL, PM_NOREMOVE));
}

ExitLoop:
```
 

대화상자의 `idle` 타임에는 `owner` 윈도우에 `WM_ENTERIDLE`을 전송하여 `owner` 윈도우가 `idle` 타임을 활용할 수 있게 한다.

 
