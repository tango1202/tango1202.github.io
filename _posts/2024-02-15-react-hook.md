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
  const onPlusOneClick = () => {
    setStateCount(stateCount + 1);
  };
  // #2. 1이 증가됩니다.
  const onPlusThreeClick = () => {
    // setStateCount(stateCount + 1); 후 즉각 stateCount의 값을 바꾸지 않습니다.
    setStateCount(stateCount + 1); // #2-1. setStateCount(0 + 1) 실행
    setStateCount(stateCount + 1); // #2-2. 여전히 stateCount는 0. setStateCount(0 + 1) 실행
    setStateCount(stateCount + 1); // #2-3. 여전히 stateCount는 0. setStateCount(0 + 1) 실행
  };
  // #3. 3이 증가됩니다.
  const onPlusThreeFuncClick = () => {
    // 대기열에 상태 변경 함수 3개가 등록됩니다.
    setStateCount((s) => s + 1); // 대기열에 상태 변경 함수 추가
    setStateCount((s) => s + 1); // 대기열에 상태 변경 함수 추가
    setStateCount((s) => s + 1); // 대기열에 상태 변경 함수 추가
  };
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

클래스 형태로 만들때 다음과 같은 생명 주기에 맞춰 해당 이벤트에서 성능 최적화를 할 수 있었습니다. 컴포넌트가 최초 실행하면 `componentDidMount()`가 호출되고, 화면 갱신시 `componentDidUpdate()`가 호출되고, 컴포넌트가 제거되면 `componentWillUnmount()`가 호출됩니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/f306f507-06cc-4b80-8c4a-abbac671f49d)

# Hook이란?

`Hook`은 함수형 컴포넌트에서 리액트의 [State](??)와 [컴포넌트 생명 주기](??)를 연동하는 함수입니다.

1. 함수명은 `use`로 시작합니다.
2. 반복문, 조건문, 중첩된 함수내에서 사용할 수 없습니다.(*동일한 [Props](??)나 [State](??)에서는 동일한 결과가 리턴되는 `Pure Function`임을 보장하기 위함입니다. [주요 개념 정리](??) 참고*)
3. 일반적인 코드 내에서 사용할 수 없고, 리액트 함수형 컴포넌트 내에서 호출 순서가 보장된 상태로 작성해야 합니다.(*주로 함수형 컴포넌트 상단에 작성합니다.*)

기본적으로 제공하는 `Hook`은 다음과 같습니다.

|항목|내용|
|--|--|
|[useState()](??)|[State](??)의 getter, setter를 제공합니다. setter로 수정하면 랜더링을 다시 합니다.|
|[useEffect()](??)|[Props](??)나 [State](??)가 변경될 때와 [컴포넌트 생명 주기](??)에 맞춰 호출됩니다.|
|[useMemo()](??)|이전에 저장된 정보를 메모리에 기억했다가 재사용합니다.|
|[useCallback()](??)|이전에 사용한 함수를 새로 만들지 않고 메모리에 기억했다가 재사용합니다.|
|[useRef()](??)|컴포넌트를 다시 렌더링하지 않는 변수를 관리하거나, JSX의 특정 DOM을 참조합니다.|
|[useContext()](??)|여러 컴포넌트에서 접근 가능한 전역적인 [State](??)입니다.|
|[useReducer()](??)|[State](??) 수정을 컴포넌트에서 분래해서 관리하게 합니다.|

# useEffect()

[useEffect()](??)는 [컴포넌트 생명 주기](??)에 따라 호출됩니다. 

첫번째 인자는 호출할 함수를 전달하며, 두번째 인자는 어느 시점에 호출할지 결정하는 의존성 배열을 전달합니다. 의존성 배열을 생략하면 렌더링이 시도될 때마다 호출됩니다.

|항목|내용|
|--|--|
|인자 없음|렌더링이 시도될 때마다 호출됩니다.|
|`[]`|최초 실행될때(*Mount 될때*) 호출됩니다.|
|`[Props 또는 State들]`|해당 값이 변경될때마다 호출됩니다.|

다음은 두번째 인자를 `인자 없음`, `[]`, `[propVal]`, `[state]`, `[propVal, state]`로 테스트한 예입니다.

1. #1 : 버튼 클릭시 `propVal`을 변경합니다.
2. #2 : 버튼 클릭시 `state`를 변경합니다.

```tsx
import { useState, useEffect } from 'react';

const MyUseEffect = () => {
  const [propVal, setPropVal] = useState(0);

  const onClick = () => {
    setPropVal(propVal + 1); // #1. propVal을 변경합니다. MyComponent 가 다시 랜더링됩니다.
  };

  return (
    <>
      <button onClick={onClick}>{'value 변경'}</button>
      <MyComponent propVal={propVal} />
    </>
  );
};

interface IMyComponentProps {
  propVal: number;
}

const MyComponent = (props: IMyComponentProps) => {
  const { propVal } = props;
  const [state, setState] = useState(0);

  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, 인자 없음. 렌더링 호출시마다 실행`));
  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, []. 최초에 1회 실행`), []);
  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, [propVal]. Props가 바뀌면 실행`), [propVal]);
  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, [state]. State가 바뀌면 실행`), [state]);
  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, [propVal, state]. Props와 State가 바뀌면 실행.`), [propVal, state]);

  const onClick = () => {
    setState(state + 1); // #2. state를 변경합니다. MyComponent 가 다시 랜더링됩니다.
  };
  return (
    <div>
      <button onClick={onClick}>{'state 변경'}</button>
      <div>{`propVal = ${propVal}, state = ${state}`}</div>
    </div>
  );
};

export default MyUseEffect;
```

또한 첫번째 인자로 전달되는 함수의 리턴값은 DOM에서 컴포넌트가 제거될때 호출됩니다. 

1. #1 : 버튼 클릭시 마다 `toggle` 상태값을 변경합니다.
2. #2 : `toggle`값 변경에 따라 `MyComponent`를 표시합니다.
3. #3 : 컴포넌트가 제거될때 호출할 함수를 리턴합니다.

```jsx
import { useState, useEffect } from 'react';

const MyUseEffectUnmount = () => {
  const [toggle, setToggle] = useState(true); // #1.

  const onToggleClick = () => {
    setToggle(!toggle); // #1. 컴포넌트를 표시 삭제를 토글합니다.
  };

  return (
    <>
      <button onClick={onToggleClick}>{'컴포넌트 삭제/추가 토글'}</button>
      {/* #2  */}
      {toggle ? <MyComponent /> : toggle}
    </>
  );
};

const MyComponent = () => {
  const [state, setState] = useState(0);

  useEffect(() => {
    console.log(`state = ${state}, []. 최초에 1회 실행`);
    return () => console.log('MyComponent가 제거됩니다.'); // #3
  }, []);

  const onClick = () => {
    setState(state + 1);
  };
  return (
    <div>
      <button onClick={onClick}>{`state 변경 ${state}`}</button>
    </div>
  );
};

export default MyUseEffectUnmount;
```

# useMemo()

[useMemo](??)는 성능 개선을 위한 훅으로서 첫번째 인자로 전달된 함수의 리턴값을 저장하고 있다가 재사용합니다. 두번째 인자는 [useEffect()](??)처럼 의존성 배열이 전달되며, 전달된 배열 요소의 값이 변경되면, 함수를 다시 호출합니다. 이를 이용하면 렌더링시 불필요한 연산을 최소화 할 수 있습니다.

1. #1 : `a`와 `b`를 더하여 리턴하는 함수입니다. 매 랜더링시에 호출됩니다.
2. #2 : 버튼 클릭시 `a`와 `b`를 `1`증가시켜 렌더링을 다시합니다.
3. #3 : 최초 실행될때(*Mount 될때*) `add()`를 호출하고 값을 저장한뒤 이 값을 사용합니다. 따라서, 처음 한번만 실행됩니다. `a`와 `b`가 `0`일때의 실행 결과값이므로 렌더링을 다시 하더라도 항상 `0`입니다.
4. #4 : `a`값이 변경될 때마다 함수를 다시 실행합니다. 따라서 `a` 버튼 클릭시마다 증가된 `a`값이 표시됩니다. 하지만, `b`가 변경되었을땐 저장된 값을 사용하므로 증가된 값이 사용되지 않습니다.
5. #5 : `a`나 `b`값이 변경될 때 함수를 다시 실행합니다. 

```tsx
import { useState, useMemo } from 'react';

const MyUseMemo = () => {
  const [a, setA] = useState(0);
  const [b, setB] = useState(0);

  const add = () => a + b; // #1
  const emptyMemo = useMemo(add, []); // #3
  const aMemo = useMemo(add, [a]); // #4
  const abMemo = useMemo(add, [a, b]); // #5

  const onStateClick = () => {
    setA(a + 1); // #2
  };
  const onValueClick = () => {
    setB(b + 1); // #2
  };
  return (
    <div>
      <button onClick={onStateClick}>{`a = ${a} 를 증가하여 렌더링을 다시 합니다.`}</button>
      <button onClick={onValueClick}>{`b = ${b} 를 증가하여 렌더링을 다시 합니다.`}</button>
      <div>{`매 랜더링시 호출. inc ${add()}`}</div>
      <div>{`최초에 1회 호출. useMemo[] ${emptyMemo}`}</div>
      <div>{`a 변경시 호출. useMemo[a] ${aMemo}`}</div>
      <div>{`a, b 변경시 호출. useMemo[a, b] ${abMemo}`}</div>
    </div>
  );
};

export default MyUseMemo;
```

# useCallback()

[useCallback](??)도 [useMemo()](??)처럼 성능 개선을 위한 훅으로서 첫번째 인자로 전달된 함수를 저장하고 있다가 재사용합니다. 두번째 인자는 [useEffect()](??)처럼 의존성 배열이 전달되며, 전달된 배열 요소의 값이 변경되면, 함수를 다시 생성합니다. 이를 이용하면 렌더링시 불필요한 함수 생성을 최소화 할 수 있습니다.

1. #1 : [State](??)인 `a`와 `b`를 더한 값을 `result`에 저장하는 함수입니다. 
2. #2 : 버튼 클릭시 `a`와 `b`를 `1`증가시켜 렌더링을 다시 합니다. 이때 `result`는 변경되지 않으므로 이전값이 표시됩니다.
3. #3 : 렌더링 시마다 함수를 다시 생성합니다. 따라서, 최신의 `a`, `b`값으로 계산합니다.
4. #4 : 최초 1회 함수를 생성합니다. 따라서, `a`, `b` 모두 초기값인 `0`일때의 값을 사용합니다.
5. #5 : `a`값이 변경될 때마다 함수를 생성합니다. 따라서 `a`값이 변경되었을때의 최종 `b`값을 사용합니다. 예를 들어 `a`와 `b`가 `0`일때 `a`를 `2`로 만든뒤, 나중에 `b`를 `2`로 만들었다면, `a`가 변경될때의 최종 `b`값은 여전히 `0`이므로 `2 + 0`이 출력됩니다. 다시 `a`를 증가시켜 `3`으로 만들면, `a`가 변경될때의 최종 `b`값은 `2`이므로 이제는 `3 + 2`가 출력됩니다.
6. #6 : `a`나 `b`값이 변경될 때 함수를 다시 생성합니다. 

```tsx
import { useState, useCallback } from 'react';

const MyUseCallback = () => {
  const [a, setA] = useState(0);
  const [b, setB] = useState(0);
  const [result, setResult] = useState(0);

  const add = () => {
    setResult(a + b); // #1
  }; 

  const onAddClick = add; // #3
  const onEmptyClick = useCallback(add, []); // #4
  const onACallbackClick = useCallback(add, [a]); // #5
  const onABCallbackClick = useCallback(add, [a, b]); // #6

  const onStateClick = () => {
    setA(a + 1); // #2
  };
  const onValueClick = () => {
    setB(b + 1); // #2
  };

  return (
    <div>
      <button onClick={onStateClick}>{`a = ${a} 를 증가`}</button>
      <button onClick={onValueClick}>{`b = ${b} 를 증가`}</button>
      <button onClick={onAddClick}>{`매 랜더링시 함수 생성. ${result}`}</button>
      <button onClick={onEmptyClick}>{`최초 1회 함수 생성. ${result}`}</button>
      <button onClick={onACallbackClick}>{`a 변경시 함수 생성. ${result}`}</button>
      <button onClick={onABCallbackClick}>{`a, b 변경시 함수 생성. ${result}`}</button>
    </div>
  );
};

export default MyUseCallback;
```

# useRef()

[useState()](??)는 컴포넌트의 상태를 관리하며, 해당 값이 수정되었을때 랜더링을 다시하는데요, [useRef()](??)는 컴포넌트의 상태를 관리하지만, 값이 수정되었을때 렌더링을 다시 하지 않습니다. 따라서, 렌더링과 상관없는 상태값을 관리할 때 유용합니다.

다음은 일반 변수, [State](??), [useRef()](??)를 비교한 예입니다.

1. #1 : [useRef()](??)로 변수를 생성합니다.
2. #2 : #2-2에서 일반 변수의 값을 증가시키더라도 렌더링시 #2-1이 실행되기 때문에 항상 `0`입니다.
3. `state`는 [State](??)이기 때문에 값이 변경되면 렌더링을 다시 합니다. 따라서 버튼 클릭시 수정된 값이 표시됩니다.
4. [useRef()](??)로 생성한 변수는 일반 변수와 달리 [State](??)처럼 컴포넌트 생명 주기에 맞춰 값이 유지되지만, 렌더링을 다시 하지 않습니다. 따라서 #3을 이용하여 렌더링을 다시 하면, 변경된 값이 표시됩니다.

```tsx
import { useState, useRef } from 'react';

const MyUseRef = () => {
  const [state, setState] = useState(0);
  const ref = useRef(0); // #1
  let value = 0; // #2-1

  const onValueClick = () => {
    value += 1; // #2-2
  };
  const onStateClick = () => {
    setState(state + 1); // #3
  };
  const onRefClick = () => {
    ref.current += 1 // #4
  };
  return (
    <div>
      <button onClick={onValueClick}>{`value = ${value} 를 증가하고 렌더링을 다시 하지 않습니다. 렌더링 하더라도 다시 0으로 초기화 됩니다.`}</button>
      <button onClick={onStateClick}>{`state = ${state} 를 증가하여 렌더링을 다시 합니다.`}</button>
      <button onClick={onRefClick}>{`ref = ${ref.current} 를 증가하고 렌더링을 다시 하지 않습니다.`}</button>
    </div>
  );
};

export default MyUseRef;
```


# useContext() - 작성중

# useReducer() - 작성중

# 커스텀 Hook





