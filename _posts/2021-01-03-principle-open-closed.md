---
layout: single
title: "#3. [개체지향 원칙] 개방-폐쇄 원칙(Open-Closed Principle)(작성중)"
categories: "principle"
tag: ["개체지향 원칙"]
author_profile: false
sidebar: 
    nav: "docs"
---

개방-폐쇄 원칙은 **확장에는 열려 있되, 수정에는 닫혀 있게 작성하라** 는 원칙입니다. 

조금 풀어 쓰면,

1. 코드의 수정없이 기능 추가나 변경이 가능하게 설계하고,

3. 요구사항 추가에 따라 새 클래스를 작성하면, 기존 클래스들이 수정 없이 유연하게 사용 가능해야 하라

라는 뜻입니다. !!!유연하게!!! 말이죠.

**해결 방법 : 유연한 함수**

SaveFile() 함수로 파일을 저장한다고 했을때, 하기와 같이 함수 내부에서 파일명을 지정했다고 생각해 보시죠.

```cpp
void SaveFile() const {
    string filename = "c:/data/my_data.txt"
    
    ...
}
```

파일명이나 경로가 바뀐다면 "SaveFile"함수를 수정해야 합니다. 조금의 변화에도 일일이 코드를 뒤져 수정한다면, 유지보수가 어려워 집니다.

다음과 같이 filename을 인자로 전달한다면, 좀더 확장성 있는 함수가 됩니다.
경로명 변경 확장에는 열려있고, SaveFile 함수 수정에는 닫혀 있게 됩니다.

```cpp

void SaveFile(const string& filename) const {

```

**해결 방법 : 유연한 개체**
IAnimal로 동물들 확장

**해결 방법 : 유연한 기능 추가**
visitAnimal로 동작들 추가

**해결 방법 : 유연한 알고리즘 변경**
strategy

