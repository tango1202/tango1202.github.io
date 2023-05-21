---
layout: single
title: "#1. 메시지 영역"
categories: "win"
tag: ["Windows", "message"]
author_profile: false
sidebar: 
    nav: "docs"
---

윈도우즈는 메시지 기반으로 프로그래밍을 하며, 다음의 메시지들이 예약된다.

 

1. 시스템 정의 영역 (0x0000~0x3FF) : 윈도우즈에서 미리 예약한 영역

 
```ini
ABM	Application desktop toolbar
BM	Button control
CB	Combo box control
CBEM	Extended combo box control
CDM	Common dialog box
DBT	Device
DL	Drag list box
DM	Default push button control
DTM	Date and time picker control
EM	Edit control
HDM	Header control
HKM	Hot key control
IPM	IP address control
LB	List box control
LVM	List view control
MCM	Month calendar control
PBM	Progress bar
PGM	Pager control
PSM	Property sheet
RB	Rebar control
SB	Status bar window
SBM	Scroll bar control
STM	Static control
TB	Toolbar
TBM	Trackbar
TCM	Tab control
TTM	Tooltip control
TVM	Tree-view control
UDM	Up-down control
WM	General window
``` 

2. 사용자 영역 (WM_USER, 0x0400~0x7FFF) : 사용자가 정의하여 사용하는 영역

3. 어플리케이션 영역(WM_APP, 0x8000~0xBFFF) : 사용자가 정의하여 사용하는 영역

4. 등록된 메시지 영역(0xC000~0xFFFF) : 머쉰에 설치된 어플리케이션끼리 동일한 메시지를 다른 용도로 사용하지 않게 하기 위하여 메시지를 등록하여 사용하는 영역(RegisterWindowMessage() 를 호출하여 리턴된 값을 메시지로 사용한다)

 