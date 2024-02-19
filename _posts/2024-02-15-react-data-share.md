---
layout: single
title: "#3. [React] 리액트 훅"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 주요 개념

* 리액트는 렌더링 컴포넌트를 트리 형태로 구성합니다.

* 동일한 [속성](??)이나 [상태](??)에서는 동일한 결과가 리턴되어야 합니다. 리액트는 이를 `Pure Function`이라고 하며, 동일한 [속성](??)이나 [상태](??)일때 동일한 JSX를 리턴한다고 가정하고 성능 최적화를 합니다. 따라서 구현시 꼭 준수해야 합니다.

* 렌더링은 [속성](??)이나 [상태](??)변경에 따라 재시도 되고, 가상 돔을 이용하여 동적으로 렌더링 요소들을 구성한뒤 변경된 것만 갱신합니다.

* [속성](??)을 이용하여 상위 컴포넌트에서 하위 컴포넌트로 데이터가 전달됩니다. 단, [속성](??)에 콜백 함수를 전달하여 역방향 흐름으로 만들 수 있습니다.

* [상태](??)를 만들어 컴포넌트의 정보를 관리할 수 있습니다. [상태](??)가 변경되면 컴포넌트를 다시 랜더링 합니다.

# 렌더링 최적화

컴포넌트는 다음 두가지 상황일때 렌더링됩니다.

1. 처음 호출되었을때 루트 컴포넌트 렌더링을 호출하고 하위 컴포넌트 렌더링을 호출합니다.
2. [속성](??)이나 [상태](??)가 변경되었을때 해당 컴포넌트 렌더링을 호출하고 하위 컴포넌트 렌더링을 호출합니다.

[속성](??)이나 [상태](??)가 변경되면, 렌더링이 대기열에 추가되며, 번경된 것들을 계산하여 가상을 DOM을 수정합니다. 이때 최신 렌더링 출력과 일치하도록 최소한의 필수 렌더링만 수행합니다.

# 스냅샷 상태

[상태](??)를 변경하면 상태값을 수정하고 JSX를 다시 렌더링합니다. 이때 다시 렌더링되기 전까지는 현 스코프에서 이전 상태값이 사용됩니다. 이 상황을 스냅샷 상태라고 합니다.

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





