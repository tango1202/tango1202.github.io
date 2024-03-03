---
layout: single
title: "#7. [React] Redux"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

리덕스는 [useContext()](https://tango1202.github.io/react/react-hook/#usecontext)처럼 공용으로 사용할 수 있는 [State](https://tango1202.github.io/react/react-basic/#state)를 제공하며, [useReducer()](https://tango1202.github.io/react/react-hook/#usereducer) 처럼 [State](https://tango1202.github.io/react/react-basic/#state) 수정 관련 로직을 응집해서 관리해 줍니다.

이런 이유로 [useContext()](https://tango1202.github.io/react/react-hook/#usecontext)나 [useReducer()](https://tango1202.github.io/react/react-hook/#usereducer)를 각각 사용하지 않고, 그냥 리덕스를 사용합니다.

# 리덕스 설치

터미널에서 다음 명령을 입력하여 [Redux(https://ko.redux.js.org/)](https://react.i18next.com/)를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

```
npm install -D redux react-redux @reduxjs/toolkit
```

# 리덕스 사용하기

[useReducer()](https://tango1202.github.io/react/react-hook/#usereducer) 에서 UI를 이용하여 `data` 정보(*`name`과 `age`입니다*)를 `CRUD`(*`Create`, `Read`, `Update`, `Delete`*)하는 예를 소개했습니다.

상위 개체에서 데이터를 [State](https://tango1202.github.io/react/react-basic/#state)로 만들고, 하위 개체에 전달해서 사용했는데요, 이를 리덕스를 이용해서 리팩토링 했습니다.

전체적인 구조는 동일하며, 다른 부분만 말씀드리자면,

1. #3 : `IAction`을 리덕스의 `Action`을 이용하여 선언합니다. 이때 `type`은 문자열만 가능합니다.
2. #4 : `state`의 기본값 인자로 초기값을 전달해야 합니다. 전달하지 않으면 `configureStore()`시 타입 오류가 발생합니다.
3. #6 : 리덕스의 `store` 개체를 생성합니다. `preloadedState`를 통해 초기값을 전달할 수 있습니다. 생성한 `store`는 #6-1와 같이 `Provider`에 전달합니다.
4. #7, #8 : 각 컴포넌트에서 리덕스를 이용하므로 [Props](https://tango1202.github.io/react/react-basic/#props)를 사용할 필요가 없습니다.
5. #7-1 : 리덕스 액션을 실행하기 위해 `dispatch`를 구합니다.
6. #7-3 : `dispatch`로 `datasReducer()`를 호출하여 액션을 실행합니다.
7. #8-1 : `useSelector()`를 이용하여 `store`에 있는 값을 읽어옵니다.

```tsx
import { useRef, useCallback } from 'react';
import {Provider, useDispatch, useSelector} from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import type { Action, Dispatch } from "redux";

// #1. redux store 에서 사용할 데이터 정보입니다.
interface IData {
  name: string;
  age: number;
}
interface IState {
  datas: IData[];
}

// #2. redux store 의 초기값입니다. 빈 배열입니다.
const initialState: IState = {
  datas: [],
};

// #3. redux 에서 사용할 액션 정보입니다. 액션 타입에 따라 동작을 구분합니다.
// useReducer() 와의 차이점 : redux의 액션 type은 문자열만 가능합니다.
type ActionType = 'Create' | 'Update' | 'Delete';
interface IAction extends Action<ActionType> { 
  data: IData; // #3-1. 액션 타입에 따라 전달된 data를 이용하여 state를 수정합니다.
}

// #4. ActionType에 따라 state의 값을 수정합니다. 이때, State의 경우와 마찬가지로 복제본을 리턴해야 다시 렌더링 됩니다.
// State를 수정하는 기능들이 한곳에 응집되어 관리가 용이해 집니다.
// useReducer() 와의 차이점 : state 기본값 인자로 초기값을 전달해야 합니다.
const datasReducer = (state: IState = initialState, action: IAction): IState => {
  switch (action.type) {
    case 'Create': // datas 뒤에 새로운 action.data를 추가하여 리턴합니다.
      return {
        ...state,
        datas: [...state.datas, action.data],
      };
    case 'Update': // datas에서 이름이 동일한 항목을 수정하여 리턴합니다.
      return {
        ...state,
        datas: state.datas.map((data) => (data.name !== action.data.name ? data : action.data)),
      };
    case 'Delete': // datas에서 이름이 동일한 항목을 삭제합니다.
      return {
        ...state,
        datas: state.datas.filter((data) => data.name !== action.data.name),
      };
    default:
      return state;
  }
};

// #5. 사용자 정보를 입력받는 Toolbar와 해당 내용을 출력하는 List를 표시합니다.
const MyRedux = () => {
  // #6. redux의 store 개체를 생성합니다. reducer 속성을 설정합니다. 
  const store = configureStore({ 
    reducer: datasReducer, 
    preloadedState : initialState
  });

   return (
    <div>
      {/* #6-1. 생성된 store를 Provider에 전달합니다. */}
      <Provider store={store}>
        <MyToolbar />
        <MyList />
      </Provider>
    </div>
  );
};

// #7. UI를 이용하여 정보를 읽고 redux 액션을 실행합니다.
// useReducer() 와의 차이점 : 각 컴포넌트에서 redux를 이용하므로 Props를 사용할 필요가 없습니다.
const MyToolbar = () => {
  const nameRef = useRef<HTMLInputElement>(null);
  const ageRef = useRef<HTMLInputElement>(null);
  const dispatch = useDispatch<Dispatch<IAction>>(); // #7-1. redux 액션을 실행하기 위해 dispatch를 구합니다.

  // #7-2. 불필요한 함수 생성을 최소화하도록 useCallback을 사용합니다.
  const createAction = useCallback((type: ActionType, name: string, age: number): IAction => {
    return {
      type,
      data: { name, age },
    };
  }, []);
  // #7-3. 입력 개체의 정보를 바탕으로 Action을 만든뒤 datasReducer()를 호출하여 액션을 실행합니다.
  const onCreate = () => {
    dispatch(createAction('Create', nameRef.current ? nameRef.current.value : '', ageRef.current ? Number(ageRef.current.value) : 0));
  };
  const onUpdate = () => {
    dispatch(createAction('Update', nameRef.current ? nameRef.current.value : '', ageRef.current ? Number(ageRef.current.value) : 0));
  };
  const onDelete = () => {
    dispatch(createAction('Delete', nameRef.current ? nameRef.current.value : '', ageRef.current ? Number(ageRef.current.value) : 0));
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

// #8. datas를 출력합니다.
// useReducer() 와의 차이점 : 각 컴포넌트에서 redux를 이용하므로 Props를 사용할 필요가 없습니다.
const MyList = () => {
  // #8-1. useSelector()를 이용하여 store에 있는 값을 읽어옵니다.
  const datas = useSelector<IState, IData[]>((state) => state.datas);
  return (
    <ol>
      {datas.map((data) => (
        <li key={data.name}> {/* #8-2. 목록형으로 출력하므로 key를 사용합니다. */}
          {data.name} {', '} {data.age}
        </li>
      ))}
    </ol>
  );
};

export default MyRedux;
```

# createSlice()와 combineReducers()

# immer



