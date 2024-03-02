---t
layout: single
title: "#3. [React] 리액트 Hook"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 스냅샷 상태

[State](https://tango1202.github.io/react/react-basic/#state)를 변경하면 [State](https://tango1202.github.io/react/react-basic/#state)값을 수정하고 JSX를 다시 렌더링합니다. 이때 다시 렌더링되기 전까지는 현 스코프에서 이전 [State](https://tango1202.github.io/react/react-basic/#state)값이 사용됩니다. 이 상황을 스냅샷 [State](https://tango1202.github.io/react/react-basic/#state)라고 합니다.

1. #1 : `countState + 1`을 한 값으로 [State](https://tango1202.github.io/react/react-basic/#state)를 변경하고 렌더링을 요청합니다.

2. #2 : `countState + 1`을 한 값으로 [State](https://tango1202.github.io/react/react-basic/#state)를 변경하고 렌더링 요청하는 것을 3회 합니다. 이때 #2-1을 실행하여 [State](https://tango1202.github.io/react/react-basic/#state)값이 갱신된 뒤에 #2-2가 실행되는게 아닙니다. `countState`는 여전히 `0`인 상태로 `countState + 1`을 수행하기 때문에 `setCountState(0 + 1)`을 3회 호출한 셈이 됩니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/19a77e41-a703-4ef2-b657-87ad732f2c9a)

    따라서, 1이 증가됩니다.

3. #3 : [State](https://tango1202.github.io/react/react-basic/#state)를 변경하는 함수를 등록합니다. 기존값에 `+ 1`을 하는 것을 3회 반복하므로, 3이 증가됩니다.

따라서, [State](https://tango1202.github.io/react/react-basic/#state)가 복잡하게 얽혀 있고 기존 [State](https://tango1202.github.io/react/react-basic/#state)로부터 수정이 필요한 경우에는 #3과 같이 [State](??)를 변경하는 함수를 사용하는 편이 좋습니다. 

```tsx
import { useState } from 'react';

const MySnapshot = () => {
  const [countState, setCountState] = useState(0);
  // #1. 1이 증가됩니다.
  const onPlusOneClick = () => {
    setCountState(countState + 1);
  };
  // #2. 1이 증가됩니다.
  const onPlusThreeClick = () => {
    // setCountState(countState + 1); 후 즉각 countState의 값을 바꾸지 않습니다.
    setCountState(countState + 1); // #2-1. setCountState(0 + 1) 실행
    setCountState(countState + 1); // #2-2. 여전히 countState는 0. setCountState(0 + 1) 실행
    setCountState(countState + 1); // #2-3. 여전히 countState는 0. setCountState(0 + 1) 실행
  };
  // #3. 3이 증가됩니다.
  const onPlusThreeFuncClick = () => {
    // 대기열에 상태 변경 함수 3개가 등록됩니다.
    setCountState((prevState) => prevState + 1); // 대기열에 상태 변경 함수 추가
    setCountState((prevState) => prevState + 1); // 대기열에 상태 변경 함수 추가
    setCountState((prevState) => prevState + 1); // 대기열에 상태 변경 함수 추가
  };
  return (
    <div>
      <button onClick={onPlusOneClick}>+1</button>
      <button onClick={onPlusThreeClick}>+3</button>
      <button onClick={onPlusThreeFuncClick}>+3 with Func</button>
      <p>countState = {countState}</p>
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

리액트의 컴포넌트는 [Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)의 값 변경 이벤트에 따라 동작합니다. 따라서, [Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)의 변경을 잘 감시할 필요가 있는데요,

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/1474566a-9b93-46b3-b627-75a9d1dce6a9)


`Hook`은 함수형 컴포넌트에서 리액트의 [Props](https://tango1202.github.io/react/react-basic/#props), [State](https://tango1202.github.io/react/react-basic/#state)와 [컴포넌트 생명 주기](https://tango1202.github.io/react/react-hook/#%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%83%9D%EB%AA%85-%EC%A3%BC%EA%B8%B0)를 연동하는 함수입니다.(*아마도 [Props](https://tango1202.github.io/react/react-basic/#props), [State](https://tango1202.github.io/react/react-basic/#state)의 변경을 Hooking하고 있어서, 이름을 `Hook`이라고 한듯 합니다.*)

1. `Hook` 함수명은 반드시 `use`로 시작합니다.
2. 반복문, 조건문, 중첩된 함수내에서 사용할 수 없습니다.(*동일한 [Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)에서는 동일한 결과가 리턴되는 `Pure Function`임을 보장하기 위함입니다. [주요 개념 정리](https://tango1202.github.io/react/react-basic/#%EC%A3%BC%EC%9A%94-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC) 참고*)
3. 일반적인 코드 내에서 사용할 수 없고, 리액트 함수형 컴포넌트 내에서 호출 순서가 보장된 상태로 작성해야 합니다.(*주로 함수형 컴포넌트 상단에 작성합니다.*)

기본적으로 제공하는 `Hook`은 다음과 같습니다.

|항목|내용|
|--|--|
|[useState()](https://tango1202.github.io/react/react-basic/#state)|[State](https://tango1202.github.io/react/react-basic/#state)의 getter, setter를 제공합니다. setter로 수정하면 렌더링을 다시 합니다.|
|[useEffect()](https://tango1202.github.io/react/react-hook/#useeffect)|[Props](https://tango1202.github.io/react/react-basic/#props)나 [State](https://tango1202.github.io/react/react-basic/#state)가 변경될 때와 [컴포넌트 생명 주기](https://tango1202.github.io/react/react-hook/#%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%83%9D%EB%AA%85-%EC%A3%BC%EA%B8%B0)에 맞춰 호출됩니다.|
|[useMemo()](https://tango1202.github.io/react/react-hook/#usememo)|이전에 저장된 정보를 메모리에 기억했다가 재사용합니다.|
|[useCallback()](https://tango1202.github.io/react/react-hook/#usecallback)|이전에 사용한 함수를 새로 만들지 않고 메모리에 기억했다가 재사용합니다.|
|[useRef()](https://tango1202.github.io/react/react-hook/#useref)|컴포넌트를 다시 렌더링하지 않는 변수를 관리하거나, JSX의 특정 DOM을 참조합니다.|
|[useContext()](??)|여러 컴포넌트에서 접근 가능한 전역적인 [State](https://tango1202.github.io/react/react-basic/#state)입니다.|
|[useReducer()](??)|[State](https://tango1202.github.io/react/react-basic/#state) 수정을 컴포넌트에서 분리할 수 있게 합니다.|

# useEffect()

[useEffect()](https://tango1202.github.io/react/react-hook/#useeffect)는 [컴포넌트 생명 주기](https://tango1202.github.io/react/react-hook/#%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%83%9D%EB%AA%85-%EC%A3%BC%EA%B8%B0)에 따라 호출됩니다. 

첫번째 인자는 호출할 함수를 전달하며, 두번째 인자는 어느 시점에 호출할지 결정하는 의존성 배열을 전달합니다. 의존성 배열을 생략하면 렌더링이 시도될 때마다 호출됩니다.

|항목|내용|
|--|--|
|인자 없음|렌더링이 시도될 때마다 호출됩니다.|
|`[]`|최초 실행될때(*Mount 될때*) 호출됩니다.|
|`[Props 또는 State들]`|해당 값이 변경될때마다 호출됩니다.|

다음은 두번째 인자를 `인자 없음`, `[]`, `[propVal]`, `[state]`, `[propVal, state]`로 테스트한 예입니다.(*테스트를 위한 코드라 의존성 배열이 맞지않아 [exhaustive-deps](https://github.com/facebook/react/issues/14920) 경고가 발생합니다. 이를 막기 위해 `// eslint-disable-next-line react-hooks/exhaustive-deps`을 사용했습니다.*)

1. #1 : 버튼 클릭시 `propVal`을 변경합니다.
2. #2 : 버튼 클릭시 `state`를 변경합니다.

```tsx
import { useState, useEffect } from 'react';

const MyUseEffect = () => {
  const [propVal, setPropVal] = useState(0);

  const onClick = () => {
    setPropVal(propVal + 1); // #1. propVal을 변경합니다. MyComponent 가 다시 렌더링됩니다.
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
  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, []. 최초에 1회 실행`),
    // eslint-disable-next-line react-hooks/exhaustive-deps
    []);
  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, [propVal]. Props가 바뀌면 실행`),
    // eslint-disable-next-line react-hooks/exhaustive-deps
    [propVal]);
  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, [state]. State가 바뀌면 실행`),
    // eslint-disable-next-line react-hooks/exhaustive-deps
    [state]);
  useEffect(() => console.log(`propVal = ${propVal}, state = ${state}, [propVal, state]. Props와 State가 바뀌면 실행.`), [propVal, state]);

  const onClick = () => {
    setState(state + 1); // #2. state를 변경합니다. MyComponent 가 다시 렌더링됩니다.
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

또한 첫번째 인자로 전달되는 함수의 리턴값은 DOM에서 컴포넌트가 제거될때 호출됩니다. 사용했던 리소스나 이벤트를 제거할때 사용하기 적당합니다. 

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
      <button onClick={onToggleClick}>{'컴포넌트 추가/삭제 토글'}</button>
      {toggle ? <MyComponent /> : null} {/* #2 */}
    </>
  );
};

const MyComponent = () => {

  useEffect(() => {
    return (
      () => { // #3
        console.log('MyComponent가 삭제됩니다.');
      }
    ); 
  }, 
  // eslint-disable-next-line react-hooks/exhaustive-deps
  []);

  return (
    <div>{'토글버튼을 클릭하면 표시되거나 삭제됩니다.'}</div>
  );
};

export default MyUseEffectUnmount;
```

# useMemo()

[useMemo](??)는 성능 개선을 위한 `Hook`으로서 첫번째 인자로 전달된 함수의 리턴값을 저장하고 있다가 재사용합니다. 두번째 인자는 [useEffect()](https://tango1202.github.io/react/react-hook/#useeffect)처럼 의존성 배열이 전달되며, 전달된 배열 요소의 값이 변경되면, 함수를 다시 호출합니다. 이를 이용하면 렌더링시 불필요한 연산을 최소화 할 수 있습니다.

1. #1 : `a`와 `b`를 더하여 리턴하는 함수입니다. 매 렌더링시에 호출됩니다.
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
  const emptyMemo = useMemo(add, 
    // eslint-disable-next-line react-hooks/exhaustive-deps
    []); // #3
  const aMemo = useMemo(add, 
    // eslint-disable-next-line react-hooks/exhaustive-deps
    [a]); // #4
  const abMemo = useMemo(add, [a, b]); // #5

  const onStateClick = () => {
    setA(a + 1); // #2
  };
  const onValueClick = () => {
    setB(b + 1); // #2
  };
  return (
    <div>
      <button onClick={onStateClick}>{`a = ${a} 를 증가시키고 렌더링을 다시 합니다.`}</button>
      <button onClick={onValueClick}>{`b = ${b} 를 증가시키고 렌더링을 다시 합니다.`}</button>
      <div>{`매 렌더링시 호출. inc ${add()}`}</div>
      <div>{`최초에 1회 호출. useMemo[] ${emptyMemo}`}</div>
      <div>{`a 변경시 호출. useMemo[a] ${aMemo}`}</div>
      <div>{`a, b 변경시 호출. useMemo[a, b] ${abMemo}`}</div>
    </div>
  );
};

export default MyUseMemo;
```

# useCallback()

[useCallback](??)도 [useMemo()](https://tango1202.github.io/react/react-hook/#usememo)처럼 성능 개선을 위한 `Hook`으로서 첫번째 인자로 전달된 함수를 저장하고 있다가 재사용합니다. 두번째 인자는 [useEffect()](https://tango1202.github.io/react/react-hook/#useeffect)처럼 의존성 배열이 전달되며, 전달된 배열 요소의 값이 변경되면, 함수를 다시 생성합니다. 이를 이용하면 렌더링시 불필요한 함수 생성을 최소화 할 수 있습니다.

1. #1 : [State](https://tango1202.github.io/react/react-basic/#state)인 `a`와 `b`를 더한 값을 `result`에 저장하는 함수입니다. 
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
  const onEmptyClick = useCallback(add,
    // eslint-disable-next-line react-hooks/exhaustive-deps
    []); // #4
  const onACallbackClick = useCallback(add,
    // eslint-disable-next-line react-hooks/exhaustive-deps
    [a]); // #5
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
      <button onClick={onAddClick}>{`매 렌더링시 함수 생성. ${result}`}</button>
      <button onClick={onEmptyClick}>{`최초 1회 함수 생성. ${result}`}</button>
      <button onClick={onACallbackClick}>{`a 변경시 함수 생성. ${result}`}</button>
      <button onClick={onABCallbackClick}>{`a, b 변경시 함수 생성. ${result}`}</button>
    </div>
  );
};

export default MyUseCallback;
```

# useRef()

[useState()](https://tango1202.github.io/react/react-basic/#state)는 컴포넌트의 상태를 관리하며, 해당 값이 수정되었을때 렌더링을 다시 하는데요, [useRef()](https://tango1202.github.io/react/react-hook/#useref)는 컴포넌트의 상태를 관리하지만, 값이 수정되었을때 렌더링을 다시 하지 않습니다. 따라서, 렌더링과 상관없는 상태값을 관리할 때 유용합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/fe2761b3-9043-4b68-89d6-66442f4cfc24)

다음은 일반 변수, [State](https://tango1202.github.io/react/react-basic/#state), [useRef()](https://tango1202.github.io/react/react-hook/#useref)를 비교한 예입니다.

1. #1 : [useRef()](https://tango1202.github.io/react/react-hook/#useref)로 변수를 생성합니다. 변수의 실제 값은 `ref.current`를 통해 접근합니다.
2. #2 : #2-2에서 일반 변수의 값을 증가시키더라도 렌더링시 #2-1이 실행되기 때문에 항상 `0`입니다.
3. #3: `state`는 [State](https://tango1202.github.io/react/react-basic/#state)이기 때문에 값이 변경되면 렌더링을 다시 합니다. 따라서 버튼 클릭시 수정된 값이 표시됩니다.
4. #4 : [useRef()](https://tango1202.github.io/react/react-hook/#useref)로 생성한 변수는 일반 변수와 달리 [State](https://tango1202.github.io/react/react-basic/#state)처럼 컴포넌트 생명 주기에 맞춰 값이 유지되지만, 렌더링을 다시 하지 않습니다. 따라서 #3을 이용하여 억지로 렌더링을 다시 하면, 변경된 값이 표시됩니다.

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
      <button onClick={onValueClick}>{`value = ${value} 를 증가시키고 렌더링을 다시 하지 않습니다. 렌더링 하더라도 다시 0으로 초기화 됩니다.`}</button>
      <button onClick={onStateClick}>{`state = ${state} 를 증가시키고 렌더링을 다시 합니다.`}</button>
      <button onClick={onRefClick}>{`ref = ${ref.current} 를 증가시키고 렌더링을 다시 하지 않습니다.`}</button>
    </div>
  );
};

export default MyUseRef;
```
# useRef()를 이용한 JSX DOM 접근

[useRef()](https://tango1202.github.io/react/react-hook/#useref)는 JSX DOM 요소에 접근하는 데에도 사용할 수 있습니다.

다음은 버튼 클릭시 `<input>`에 접근하여 포커스를 주는 예입니다.

1. [useRef()](https://tango1202.github.io/react/react-hook/#useref)로 `HTMLInputElement` 타입의 개체를 생성합니다. 이 개체는 #1-1 과 같이 `ref={nameRef}`하여 초기화 합니다.

    ![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/9ad959e5-2484-4b30-a2be-48dcba9d953e)

2. 버튼 클릭시 `nameRef.current.focus()`하여 `input`개체에 포커스를 줍니다.

```tsx
import { useRef } from 'react';

const MyUseRefFocus = () => {
  const nameRef = useRef<HTMLInputElement>(null); // #1

  const onClick = () => {
    if (nameRef.current !== null) {
      nameRef.current.focus(); // #2
    }
  };

  return (
    <div>
      <input placeholder="아이디" />
      <input placeholder="이름" ref={nameRef} /> {/* #1-1 */}
      <button onClick={onClick}>{'이름에 포커스를 줍니다.'}</button>
    </div>
  );
};

export default MyUseRefFocus;
```

# useRef()를 이용한 사용자 정의 컴포넌트 메서드 접근

사용자 정의 컴포넌트의 경우는 `ref` 속성이 없으므로 `forwardRef()`함수를 이용하여 `ref` 속성을 만들어 주어야 합니다. 또한, `useImperativeHandle()`을 이용하여 컴포넌트의 메서드를 이용할 수 있습니다.

1. #1 : [useRef()](https://tango1202.github.io/react/react-hook/#useref)로 개체를 생성합니다. 이 개체는 #1-1 과 같이 `ref={myCounterRef}`하여 초기화 합니다.
2. #2 : `MyCounter`개체 `ref`의 인터페이스 입니다.
3. #3 : `forwardRef()`를 이용해서 외부에서 `ref` 속성을 사용할 수 있게 합니다.
4. #4 : 외부에서 실행할 함수입니다. `setValue()`를 이용하여 [State](??)를 수정합니다.
5. #5 : `useImperativeHandle()`을 이용하여 외부에 노출할 함수들을 모아 개체로 리턴합니다.
6. #6 : `ref`를 이용하여 `MyCounter`개체의 메서드를 호출합니다.

```tsx
import { useState, useRef, forwardRef, useImperativeHandle } from 'react';

const MyUseRefCustom = () => {
  const myCounterRef = useRef<IMyCounterRef>(null); // #1

  const onRefClick = () => {
    console.log(30);
    if (myCounterRef.current !== null) {
      myCounterRef.current.setValueFunc(30); // #6
    }
  };

  return (
    <div>
      <button onClick={onRefClick}>{'ref를 이용하여 30으로 세팅합니다.'}</button>
      <MyCounter ref={myCounterRef} /> {/* #1-1 */}
    </div>
  );
};

// #2. 외부로 노출할 함수들로 구성된 인터페이스입니다.
interface IMyCounterRef {
  setValueFunc(val: number): void;
}
// #3. forwardRef를 이용해서 외부에서 ref 속성을 사용할 수 있게 합니다.
const MyCounter = forwardRef<IMyCounterRef>((props, ref) => {
  const [value, setValue] = useState(0);

  // #4. 외부에서 실행할 함수입니다. setValue()를 이용하여 State를 수정합니다.
  const setValueFunc = (val: number): void => setValue(val);

  // #5. 외부에 노출할 함수들을 모아 개체로 리턴합니다.
  useImperativeHandle(ref, (): IMyCounterRef => {
    return {
      setValueFunc,
    };
  });
  const onMinusClick = () => {
    setValue(value - 1);
  };
  const onPlusClick = () => {
    setValue(value + 1);
  };

  return (
    <>
      <button onClick={onMinusClick}>{'-'}</button>
      <span>{value}</span>
      <button onClick={onPlusClick}>{'+'}</button>
    </>
  );
});

export default MyUseRefCustom;
```

# useContext()

리액트는 [Props](https://tango1202.github.io/react/react-basic/#props)를 이용하여 상위 컴포넌트에서 하위 컴포넌트로 데이터를 전달합니다. 이러다 보니 계층이 깊은 경우 서로 데이터를 전달하기 위해서는 불필요하게 [Props](https://tango1202.github.io/react/react-basic/#props)를 포워딩하는 과정이 필요합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/be520bb3-15b6-4a68-942b-9e76bf4ab935)

[useContext()](??)는 공용으로 사용할 수 있는 [State](??)를 만들어 여러 컴포넌트에서 공유할 수 있게 해줍니다.(*[Redux](??)도 동일한 용도로 사용하며, 좀더 많은 기능을 제공합니다.*)

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/12374dd2-bc72-4213-ae79-3706a5a3f202)

다음은 `isDark`값에 따라 컴포넌트의 스킨을 변경하는 예입니다. 각 컴포넌트가 [useContext()](??)를 사용하여 값을 공유하므로, 불필요하게 [Props](https://tango1202.github.io/react/react-basic/#props)를 포워딩할 필요가 없습니다.

1. #1 : 공유해서 사용할 저장소 인터페이스 입니다.
2. #2 : `createContext()`로 저장소를 생성합니다. 
3. #3 : JSX에서 저장소를 공유할 컴포넌트의 최상위에 `<MyThemeContext.Provider>`를 작성합니다. 이때 #3-1의 [State](??)와 연결합니다.
4. #4 : [useContext()](??)를 이용하여 저장소를 이용할 수 있습니다.
5. #5 : 저장소에서 읽은 `MyThemeContext` 값에 따라 스타일을 바꿉니다.
6. #6 : #3-1인 [State](??)를 수정하던, `MyThemeContext`의 `setIsDark()`를 사용하던 모두 동일한 값을 수정합니다.

```tsx
import { useState, createContext, useContext } from 'react';

interface IMyThemeContext { // #1. 공유해서 사용할 저장소 인터페이스 입니다. 
  isDark: boolean;
  setIsDark?(isDark: boolean): void;
}
const MyThemeContext = createContext<IMyThemeContext>({ isDark: false}); // #2. 저장소를 생성합니다. 기본값을 전달합니다.

const MyUseContext = () => {
  const [isDark, setIsDark] = useState(false); // #3-1
  const onClick = () => { 
    setIsDark(!isDark); // #6. MyThemeContext의 값을 수정합니다.
  };
  return (
    <div>
      <button onClick={onClick}>{'다크 모드 토글'}</button>
      <MyThemeContext.Provider value={{ isDark: isDark, setIsDark: setIsDark }}> {/* #3. Provider 하위의 모든 개체에 전달할 값을 설정합니다. */}
        <MyToolbar />
      </MyThemeContext.Provider>
    </div>
  );
};

const MyToolbar = () => {
  const { isDark, setIsDark } = useContext<IMyThemeContext>(MyThemeContext); // #4. MyThemeContext의 값을 사용합니다.

  const onClick = () => {
    if (setIsDark !== undefined) {
      setIsDark(!isDark); // #6. MyThemeContext의 값을 수정합니다.
    }
  };
  return (
    <div
      style={{ // #5. MyThemeContext 값에 따라 스타일을 바꿉니다.
        backgroundColor: isDark ? 'black' : 'white',
      }}
    >
      <span
        style={{ // #5. MyThemeContext 값에 따라 스타일을 바꿉니다.
          backgroundColor: isDark ? 'black' : 'white',
          color: isDark ? 'white' : 'black',
        }}
      >
        {'툴바 :'}
      </span>
      <MyButton caption={'버튼1'} />
      <MyButton caption={'하위 컴포넌트에서 다크 모드 토글'} onClick={onClick} />
      <MyButton caption={'버튼1'} />
    </div>
  );
};

interface IProps {
  caption: string;
  onClick?(): void;
}
const MyButton = (props: IProps) => {
  const { isDark } = useContext<IMyThemeContext>(MyThemeContext); // #4. MyThemeContext의 값을 사용합니다.
  return (
    <button
      onClick={props.onClick}
      style={{ // #5. MyThemeContext 값에 따라 스타일을 바꿉니다.
        backgroundColor: isDark ? 'black' : 'white',
        color: isDark ? 'white' : 'black',
      }}
    >
      {props.caption}
    </button>
  );
};

export default MyUseContext;
```

# useReducer()

[State](??)의 관리는 해당 상태를 관리하는 각 컴포넌트에서 해야 합니다. 이러다 보면, 상태 수정 관련 로직이 흩어져 관리가 어려울 수 있는데요, 리액트에서는 [useReducer()](??) 를 이용하여 이를 응집해서 관리할 수 있습니다.(*[Redux](??)도 동일한 용도로 사용하며, 좀더 많은 기능을 제공합니다.*)

[useReducer()](??)는 다음 개체들이 상호 작용하면서 작동합니다.

![image](https://github.com/tango1202/tango1202.github.io/assets/133472501/83c158f8-bb4d-42e2-8571-c047255a80b0)

* `action` : [State](??)를 수정하는 기능입니다.
* `reducer()` : `action`들의 집합입니다. 
* `dispatch()` : `reducer()`를 호출하여 `action`을 실행합니다.

[useReducer()](??)는 `reducer()`를 인자로 전달받아 [State](??)와 `dispatch()`를 리턴합니다. [State](??)로 데이터에 접근할 수 있으며, `dispatch()`로 [State](??)를 수정할 수 있습니다.

다음은 UI를 이용하여 `data` 정보(*`name`과 `age`입니다*)를 `CRUD`(*`Create`, `Read`, `Update`, `Delete`*)하는 예입니다.

1. #1 : `reducer()`에서 사용할 데이터 정보입니다.
2. #2 : `reducer()`의 초기값입니다. 빈 배열입니다.
3. #3 : `reducer()`에서 사용할 액션 정보입니다. 액션 타입에 따라 동작을 구분합니다.
4. #4 : `reducer()`입니다. `(state: IState, action: IAction)` 와 같이 인자로 [State](??)와 액션이 전달됩니다. 이처럼 [State](??)를 수정하는 액션들이 한곳에 응집되기 때문에 관리가 용이해 집니다.

    액션의 타입은 제공되지 없으며, 조건문으로 분기해서 실행될 수 있도록 본인이 설계해서 작성해야 합니다. 본 예에서는 `ActionType`에 따라 실행하도록 만들었습니다.

    `reducer()`가 리턴하는 값은 [State](??)의 경우와 마찬가지로 복제본이어야 합니다. 그래야 다시 렌더링 됩니다. 

5. #5 : `MyUseReducer`에서 사용자 정보를 입력받는 `MyToolbar`와 해당 내용을 출력하는 `MyList`를 표시합니다. #5-2와 같이 이벤트가 발생하면 `dispatch()`를 이용하여 액션을 실행합니다.
6. #6 : `MyToolbar`에서 UI를 이용하여 정보를 읽고 액션 개체를 만들어 상위 개체에 전달합니다. 이때 6-1과 같이 [useCallback()](??)을 이용하여 불필요한 함수 생성을 최소화하였습니다.
7. #7 : `MyList`에서 [State](??)의 `datas`를 출력합니다.

```tsx
import { useRef, useCallback, useReducer } from 'react';

// #1. reducer에서 사용할 데이터 정보입니다.
interface IData {
  name: string;
  age: number;
}
interface IState {
  datas: IData[];
}

// #2. reducer의 초기값입니다. 빈 배열입니다.
const initialState: IState = {
  datas: [],
};

// #3. reducer에서 사용할 액션 정보입니다. 액션 타입에 따라 동작을 구분합니다.
enum ActionType {
  Create,
  Update,
  Delete,
}
interface IAction {
  type: ActionType;
  data: IData; // #3-1. 액션 타입에 따라 전달된 data를 이용하여 state를 수정합니다.
}

// #4. ActionType에 따라 state의 값을 수정합니다. 이때, State의 경우와 마찬가지로 복제본을 리턴해야 다시 렌더링 됩니다. 
// State를 수정하는 기능들이 한곳에 응집되어 관리가 용이해 집니다.
const reducer = (state: IState, action: IAction): IState => {
  switch (action.type) {
    case ActionType.Create: // datas 뒤에 새로운 action.data를 추가하여 리턴합니다.
      return {
        ...state,
        datas: [...state.datas, action.data],
      };
    case ActionType.Update: // datas에서 이름이 동일한 항목을 수정하여 리턴합니다.
      return {
        ...state,
        datas: state.datas.map((data) => (data.name !== action.data.name ? data : action.data)),
      };
    case ActionType.Delete: // datas에서 이름이 동일한 항목을 삭제합니다.
      return {
        ...state,
        datas: state.datas.filter((data) => data.name !== action.data.name),
      };
    default:
      return state;
  }
};

// 5. 사용자 정보를 입력받는 Toolbar와 해당 내용을 출력하는 List를 표시합니다.
const MyUseReducer = () => {
  const [state, dispatch] = useReducer(reducer, initialState); // #5-1. reducer를 생성합니다.

  const onAction = (action: IAction) => {
    dispatch(action); // #5-2. reducer를 실행합니다.
  };
  return (
    <div>
      <MyToolbar datas={state.datas} onAction={onAction} />
      <MyList datas={state.datas} />
    </div>
  );
};

interface IMyToolbarProps {
  datas: IData[];
  onAction(action: IAction): void;
}
// 6. UI를 이용하여 정보를 읽고 Action개체를 만들어 상위 개체에 전달합니다.
const MyToolbar = (props: IMyToolbarProps) => {
  const nameRef = useRef<HTMLInputElement>(null);
  const ageRef = useRef<HTMLInputElement>(null);

  // #6-1. 불필요한 함수 생성을 최소화하도록 useCallback을 사용합니다.
  const createAction = useCallback((type: ActionType, name: string, age: number): IAction => {
    return {
      type,
      data: { name, age },
    };
  }, []);
  // #6-2. 입력 개체의 정보를 바탕으로 Action을 만든뒤 상위 개체에 전달합니다.
  const onCreate = () => {
    props.onAction(createAction(ActionType.Create, nameRef.current ? nameRef.current.value : '', ageRef.current ? Number(ageRef.current.value) : 0));
  };
  const onUpdate = () => {
    props.onAction(createAction(ActionType.Update, nameRef.current ? nameRef.current.value : '', ageRef.current ? Number(ageRef.current.value) : 0));
  };
  const onDelete = () => {
    props.onAction(createAction(ActionType.Delete, nameRef.current ? nameRef.current.value : '', ageRef.current ? Number(ageRef.current.value) : 0));
  };
  return (
    <div>
      <span>{'이름'}</span>
      <input ref={nameRef} />
      <span>{'나이'}</span>
      <input ref={ageRef} />
      <button onClick={onCreate}>{'Create'}</button>
      <button onClick={onUpdate}>{'Update'}</button>
      <button onClick={onDelete}>{'Delete'}</button>
    </div>
  );
};

interface IMyListProps {
  datas: IData[];
}
// #7. datas를 출력합니다.
const MyList = (props: IMyListProps) => {
  const { datas } = props;
  return (
    <ol>
      {datas.map((data) => (
        <li key={data.name}> {/* #7-1. 목록형으로 출력하므로 key를 사용합니다. */}
          {data.name} {', '} {data.age}
        </li>
      ))}
    </ol>
  );
};

export default MyUseReducer;
```

# 커스텀 Hook

다음은 좌우에 `+`, `-` 버튼이 있는 `MyLeftRightCounter`와 상하에 `+`, `-` 버튼이 있는 `MyTopDownCounter`를 구현한 예입니다.

비록 한줄씩이지만, `useState()`를 사용하는 부분과 증감시키기 위해 `setValue()`를 호출하는 부분이 중복됩니다. 

```tsx
import { useState } from 'react';

const MyUseCounter = () => {
  return (
    <div>
      <MyLeftRightCounter />
      <MyTopDownCounter />
    </div>
  );
};

const MyLeftRightCounter = () => {
  const [value, setValue] = useState(0); // 로직이 중복됩니다.

  const onMinusClick = () => {
    setValue(value - 1); // 로직이 중복됩니다.
  };
  const onPlusClick = () => {
    setValue(value + 1); // 로직이 중복됩니다.
  };

  return (
    <>
      <button onClick={onMinusClick}>{'-'}</button> 
      <span>{value}</span> 
      <button onClick={onPlusClick}>{'+'}</button> 
    </>
  );
};
const MyTopDownCounter = () => {
  const [value, setValue] = useState(0); // 로직이 중복됩니다.

  const onMinusClick = () => {
    setValue(value - 1); // 로직이 중복됩니다.
  };
  const onPlusClick = () => {
    setValue(value + 1); // 로직이 중복됩니다.
  };

  return (
    <>
      <div>
        <button onClick={onMinusClick}>{'-'}</button>
      </div>
      <div>{value}</div> 
      <div>
        <button onClick={onPlusClick}>{'+'}</button> 
      </div>
    </>
  );
};

export default MyUseCounter;
```

어떤 값을 [State](??)로 관리하고, `1`씩 증감 시키는게 동일하니 이부분을 분리하는게 좋은데요, 이러한 로직의 분리를 커스텀 `Hook`을 이용하여 만들 수 있습니다.

1. #1 : `useCounter()` 커스텀 `Hook`을 만듭니다. 이름은 반드시 `use`로 시작하여야 하며, 로직을 구현한 [State](??)나 함수를 개체로 묶어 리턴합니다.
2. #2 : `useCounter()`를 호출하여 값을 증감시키는 로직을 전달받습니다.
3. #3 : `inc`/`dec`를 바로 이벤트 핸들러로 등록하여 사용할 수 있습니다.

```tsx
import { useState } from 'react';

const MyUseCounter = () => {
  return (
    <div>
      <MyLeftRightCounter />
      <MyTopDownCounter />
    </div>
  );
};

// #1. 커스텀 Hook입니다. 값을 수정할 수 있는 inc(), dec()를 리턴합니다.
const useCounter = (init:number) => {
  const [value, setValue] = useState(init);
  const inc = () => {
    setValue(value + 1);
  };  
  const dec = () => {
    setValue(value - 1);
  }; 
  return {value, inc, dec}; // 로직을 구현한 State나 함수를 개체로 묶어 리턴합니다.
};

const MyLeftRightCounter = () => {
  const {value, inc, dec} = useCounter(0); // #2

  return (
    <>
      <button onClick={dec}>{'-'}</button> {/* #3. inc/dec를 바로 이벤트 핸들러로 등록하였습니다. */}
      <span>{value}</span> 
      <button onClick={inc}>{'+'}</button> {/* #3. inc/dec를 바로 이벤트 핸들러로 등록하였습니다. */} 
    </>
  );
};
const MyTopDownCounter = () => {
  const {value, inc, dec} = useCounter(0); // #2

  return (
    <>
      <div>
        <button onClick={dec}>{'-'}</button> {/* #3. inc/dec를 바로 이벤트 핸들러로 등록하였습니다. */}
      </div>
      <div>{value}</div> 
      <div>
        <button onClick={inc}>{'+'}</button> {/* #3. inc/dec를 바로 이벤트 핸들러로 등록하였습니다. */}
      </div>
    </>
  );
};

export default MyUseCounter;
```







