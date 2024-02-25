---
layout: single
title: "#3. [React] 리액트 훅"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 스냅샷 상태

[State](??)를 변경하면 상태값을 수정하고 JSX를 다시 렌더링합니다. 이때 다시 렌더링되기 전까지는 현 스코프에서 이전 상태값이 사용됩니다. 이 상황을 스냅샷 상태라고 합니다.

1. #1 : `stateCount + 1`을 한 값으로 상태를 변경하고 렌더링을 요청합니다.

2. #2 : `stateCount + 1`을 한 값으로 상태를 변경하고 렌더링 요청하는 것을 3회 합니다. 이때 #2-1을 실행하여 상태값이 갱신된 뒤에 #2-2가 실행되는게 아닙니다. `stateCount`는 여전히 `0`인 상태로 `stateCount + 1`을 수행하기 때문에 `setStateCount(0 + 1)`을 3회 호출한 셈이 됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/19a77e41-a703-4ef2-b657-87ad732f2c9a)

    따라서, 1이 증가됩니다.

3. #3 : 상태를 변경하는 함수를 등록합니다. 기존값에 `+ 1`을 하는 것을 3회 반복하므로, 3이 증가됩니다.

```tsx
import { useState } from 'react';

const MySnapshot = () => {
  const [stateCount, setStateCount] = useState(0);
  // #1. 1이 증가됩니다.
  function onPlusOneClick() {
    setStateCount(stateCount + 1);
  }
  // #2. 1이 증가됩니다.
  function onPlusThreeClick() {
    // setStateCount(stateCount + 1); 후 즉각 stateCount의 값을 바꾸지 않습니다.
    setStateCount(stateCount + 1); // #2-1. setStateCount(0 + 1) 실행
    setStateCount(stateCount + 1); // #2-2. 여전히 stateCount는 0. setStateCount(0 + 1) 실행
    setStateCount(stateCount + 1); // #2-3. 여전히 stateCount는 0. setStateCount(0 + 1) 실행
  }
  // #3. 3이 증가됩니다.
  function onPlusThreeFuncClick() {
    // 대기열에 상태 변경 함수 3개가 등록됩니다.
    setStateCount((s) => s + 1); // 대기열에 상태 변경 함수 추가
    setStateCount((s) => s + 1); // 대기열에 상태 변경 함수 추가
    setStateCount((s) => s + 1); // 대기열에 상태 변경 함수 추가
  }
  return (
    <div>
      <button onClick={onPlusOneClick}>+1</button>
      <button onClick={onPlusThreeClick}>+3</button>
      <button onClick={onPlusThreeFuncClick}>+3 with Func</button>
      <p>stateCount = {stateCount}</p>
    </div>
  );
};
export default MySnapshot;
```

# 컴포넌트 생명 주기

리액트 컴포넌트를 클래스 형태로 만들 수도 있는데요, 현재는 함수형으로 만드는 것으로 변경되어 잘 사용하지 않습니다.

클래스 형태로 만들때 다음과 같은 생명 주기에 맞춰 해당 이벤트에서 성능 최적화를 할 수 있었습니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f306f507-06cc-4b80-8c4a-abbac671f49d)

# Hook이란?

`Hook`은 함수형 컴포넌트에서 리액트의 [State](??)와 [컴포넌트 생명 주기](??)를 연동하는 함수입니다.

1. `useState()`
2. `useEffect()`
3. `useContext()`
4. `useReducer()`
5. `useMemo()`
6. `useCallback()`
7. `useRef()`

# 커스텀 Hook





