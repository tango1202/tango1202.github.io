---
layout: single
title: "#7. [고전 C++ 가이드] 상수(Const), Volatile"
categories: "classic-cpp-guide"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * `const` 정확성을 지켜라.
> * 무조건 `const`를 들이대라.
> * `const` 변수와 `const` 함수는 메모리를 수정하지 않기 때문에 예외가 발생하지 않는다.

**개요**

**Volatile**
최적화를 막음.
어셈에서 기계어에서 동일한 주소에 쓰면 최적화로 마지막것
만 반영할 수 있음. 휘발성이니 최적화 하지 말라고 지시하는 것임.

```cpp
int main()
{
	volatile unsigned int* led_control = new unsigned int;
	*led_control = 0x0001;
	*led_control = 0x0010;
	*led_control = 0x0100;

	return 0;
}
```
