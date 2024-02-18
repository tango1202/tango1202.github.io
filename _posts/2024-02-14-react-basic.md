---
layout: single
title: "#2. [React] 기본 문법"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 컴포넌트

리액트는 마크업을 리턴하는 컴포넌트의 집합으로 구성됩니다. 

`src`폴더에 `MyDiv.tsx`파일을 생성하고 다음과 같이 작성합니다.

```tsx
// 사용자 정의 태그입니다.
function MyDiv() {
  return <div>안녕하세요</div>;
}
export default MyDiv; // 내보내기 합니다.
```

`MyDiv`는 사용자가 정의한 마크업 태그명입니다. 사용하려는 곳에서 `MyDiv.tsx`를 `import` 하여 사용할 수 있습니다.

`App.tsx`에 다음과 같이 `import MyDiv from './MyDiv';`하여 `import` 하고 여러번 사용할 수 있습니다.

```tsx
import React from 'react';
import logo from './logo.svg';
import './App.css';
import MyDiv from './MyDiv'; // 불러오기 합니다.

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>안녕하세요.반가워욘</p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
        <MyDiv />
        <MyDiv />
        <MyDiv />
      </header>
    </div>
  );
}

export default App;
```

컴포넌트는 관습적으로 파스칼 표기법으로 작성하며, 다음과 같이 화살표 함수를 사용할 수도 있습니다.

```tsx
const MyDiv = () => {
  return <div>안녕하세요</div>;
};
export default MyDiv; // 내보내기 합니다.
```

`test`폴더 하위에 `MyDiv.test.tsx` 파일을 만들어 다음과 같이 테스트 할 수 있습니다.(*리액트 컴포넌트 테스트를 할 경우 확장자를 `tsx`로 해야 합니다. `ts`로 할 경우 단순한 타입스크립트로 판단하기 때문에 오동작할 수 있습니다.*)

```tsx
import { render, screen } from '@testing-library/react';
import MyDiv from '../MyDiv';

test('MyDiv 테스트', () => {
  render(<MyDiv />);
  const element = screen.getByText('안녕하세요');
  expect(element).toBeInTheDocument();
});
```

# JSX

컴포넌트가 리턴하는 마크업을 JSX라고 합니다.

  * HTML 보다 엄격하게 닫는 태그를 검사합니다.
  * 마크업 내부에 `{}`을 사용하여 코드 표현식을 사용합니다.

```tsx
const MyVal = () => {
  const val = '안녕';
  return <div>{val}</div>;
};
export default MyVal; 
```

코드 표현식을 사용하면 특정 조건에 따라 마크업을 손쉽게 만들 수 있습니다.

```tsx
<div> 
  { isOk && <MyDiv /> } 
</div>
```

데이터를 기반으로 마크업을 만들 수 있습니다.

```tsx
const MyUserList = () => {
  // 데이터 입니다.
  const users = [
    { id: 0, name: 'Kim' },
    { id: 1, name: 'Lee' },
    { id: 2, name: 'Park' },
  ];

  // 데이터로 마크업을 만듭니다.
  const listItems = users.map((user) => {
    return <li key={user.id}>{user.name}</li>;
  });
  
  return <ul>{listItems}</ul>;
};
export default MyUserList;
```

# 이벤트

컴포넌트 개체의 이벤트를 받을 수 있습니다.

```tsx
const MyButton = () => {
  function onClick() {
    alert('버튼을 클릭했습니다.');
  }

  return <button onClick={onClick}>클릭해 주세요.</button>;
};
export default MyButton;
```

# 상태

다음 예에서 `onClick()`이벤트 핸들러는 `++count;`로 `count`변수값을 증가시킵니다. 

이때 `onClick()`이벤트 핸들러는 [클로저](https://tango1202.github.io/javascript/javascript-basic/#%ED%81%B4%EB%A1%9C%EC%A0%80%EC%99%80-%EC%A0%95%EB%B3%B4-%EC%9D%80%EB%8B%89)이기 때문에 외부의 `count`변수에 접근하고 사용할 수 있습니다. 그래서 증가된 값을 잘 표시하죠.

하지만 JSX의 `<p>`가 표시하는 것은 초기에 전달한 `0`입니다. 또한 버튼을 클릭했을때 다시 랜더링하지도 않고요.

```tsx
const MyState = () => {
  let count = 0;
  console.log('MyState 이 호출되었습니다.');
  function onClick() {
    ++count;
    // 증가한 값을 잘 표시합니다.
    alert(`버튼을 ${count}회 클릭했습니다.`);
  }

  return (
    <div>
      <button onClick={onClick}>카운트합니다.</button>
      <p>
      {/* 계속 0을 표시합니다. */}
        count = {count}
      </p>
    </div>
  );
};
export default MyState;
```

이런 경우 `state`사용합니다. `state`는 컴포넌트에서 사용하는 내부 데이터로서, 값이 수정되면 수정된 값으로 JSX를 다시 렌더링 합니다. [Observer 패턴](https://tango1202.github.io/pattern/pattern-observer/)과 유사하죠. 

1. #1 : `state`를 사용하기 위해 `useState`를 가져옵니다.
2. #2 : `stateCount` 상태 변수를 선언하고, `setStateCount()` 함수를 선언합니다. 이때 초기값은 `0`을 사용합니다.
3. #3 : 상태 변수를 수정할때는 항상 `setStateCount`와 같이 `setter`함수를 이용합니다.
4. #4 : `count`는 변하지 않고, `stateCount`는 변합니다.

```tsx
import { useState } from 'react'; // #1

const MyState = () => {

  const [stateCount, setStateCount] = useState(0); // #2
  let count = 0;
  console.log('MyState 이 호출되었습니다.');
  function onClick() {
    ++count;
    // ++stateCount; // #3. state는 setter를 이용해서 수정해야만 랜더링을 다시합니다.
    setStateCount(stateCount + 1);
    alert(`버튼을 ${count}회 클릭했습니다.`);
  }

  return (
    <div>
      <button onClick={onClick}>카운트합니다.</button>
      <p>
        {/* #4. count는 변하지 않고, stateCount는 변합니다. */}
        count = {count} stateCount = {stateCount} 
      </p>
    </div>
  );
};
export default MyState;
```
