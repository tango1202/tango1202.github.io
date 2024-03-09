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

# combineReducers()

개별적으로 리듀서를 작성하고 이를 합쳐서 사용할 수 있습니다. 

다음은 사용자 이름을 저장하는 리듀서와 뭔가 값이 변경되었을때 dirty 플래그를 저장하는 리듀서를 함께 사용하는 예입니다. 이름이 추가되면 dirty 플래그를 `true`로 설정하고 붉은색 배경으로 표시하며, 저장하면 dirty 플래그를 `false`로 설정하고 파란색 배경으로 표시합니다. 

1. #1 : `namesReducer`는 사용자 이름을 관리하는 리듀서입니다.
2. #2 : `dirtyReducer`는 데이터가 수정되었는지 여부를 관리하는 리듀서 입니다.
3. #3 : 두개의 리듀서가 사용하는 [State](??)가 리덕스에 저장되는 형태입니다.
4. #4 : `combineReducers()`로 두개의 리듀서를 합칩니다. 이때 `IStore` 속성에 맞게 동일한 이름을 사용합니다.
5. #5-1, #5-2 : 이벤트에 맞춰 이름을 추가하거나, dirty 플래그를 설정합니다.
6. #6 : `useSelector()`를 이용하여 리덕스의 정보를 읽어옵니다.
7. #7 : `dirty`상태에 따라 빨간색 혹은 파란색으로 표시합니다.

```tsx
import { useRef } from 'react';
import { Provider, useDispatch, useSelector } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import { Action, combineReducers } from 'redux';

// ----
// #1. 사용자 이름을 관리하는 리듀서 입니다.
// ----
interface INamesState {
  names: string[];
}

const namesInitialState: INamesState = {
  names: ['홍길동', '성춘향'],
};

type NamesActionType = 'Add' | 'Save';
interface INamesAction extends Action<NamesActionType> {
  name: string;
}
const namesReducer = (state: INamesState = namesInitialState, action: INamesAction): INamesState => {
  switch (action.type) {
    case 'Add': // #1-1. names 뒤에 새로운 action.names를 추가하여 리턴합니다.
      return {
        ...state,
        names: [...state.names, action.name],
      };
    case 'Save': // #1-2. 무언가를 저장합니다.
      console.log('이름들을 저장합니다.');
      return state;
    default:
      return state;
  }
};

// ----
// #2. 데이터가 수정되었는지 여부를 관리하는 리듀서 입니다.
// ----
interface IDirtyState {
  dirty: boolean;
}
const dirtyInitialState: IDirtyState = {
  dirty: false,
};

type DirtyActionType = 'SetDirty';
interface IDirtyAction extends Action<DirtyActionType> {
  dirty: boolean;
}

const dirtyReducer = (state: IDirtyState = dirtyInitialState, action: IDirtyAction): IDirtyState => {
  switch (action.type) {
    case 'SetDirty':
      return {
        ...state,
        dirty: action.dirty,
      };
    default:
      return state;
  }
};

// ----
// #3. 리덕스에 저장되는 형태입니다.
// ----
interface IStore {
  namesState: INamesState;
  dirtyState: IDirtyState;
}

const MyCombineReducer = () => {
  // #4. 두개의 리듀서를 합칩니다.
  // 이때 각 리듀서는 IStore 속성에 맞춰 저장할 수 있도록 동일한 이름을 사용합니다.
  const rootReducer = combineReducers({
    namesState: namesReducer,
    dirtyState: dirtyReducer,
  });

  const store = configureStore({
    reducer: rootReducer,
  });

  return (
    <div>
      <Provider store={store}>
        <MyToolbar />
        <MyList />
      </Provider>
    </div>
  );
};

const MyToolbar = () => {
  const nameRef = useRef<HTMLInputElement>(null);
  const dispatch = useDispatch();

  // #5-1. 이름을 추가하고, dirty를 true로 설정합니다.
  const onAdd = () => {
    dispatch({
      type: 'Add',
      name: nameRef.current ? nameRef.current.value : '',
    });
    dispatch({
      type: 'SetDirty',
      dirty: true,
    });
  };
  // #5-2. dirty를 false로 설정합니다.
  const onSave = () => {
    dispatch({
      type: 'Save',
      name: '', // name 정보는 사용하지 않습니다.
    });
    dispatch({
      type: 'SetDirty',
      dirty: false,
    });
  };
  return (
    <div>
      <span>{'(combineReducer 테스트) 이름'}</span>
      <input ref={nameRef} />
      <button onClick={onAdd}>{'추가'}</button>
      <button onClick={onSave}>{'저장'}</button>
    </div>
  );
};

const MyList = () => {
  // #6. useSelector()를 이용하여 store에 있는 값을 읽어옵니다.
  const names = useSelector((store: IStore) => store.namesState.names);
  const dirty = useSelector((store: IStore) => store.dirtyState.dirty);

  return (
    //#7. dirty 상태에 따라 빨간색 혹은 파란색으로 표시합니다.
    <ol>
      {names.map((name) => (
        <li
          key={name}
          style={
            {
              backgroundColor: dirty ? 'red' : 'blue',
            }
          }
        >
          {name}
        </li>
      ))}
    </ol>
  );
};

export default MyCombineReducer;
```

# createSlice()

`Slice`는 여러개의 리듀서를 사용할 경우 이름, 초기값, 액션, 리듀서를 응집해 줍니다. 

특히, 

* 액션 타입으로 액션을 구분하지 않고 함수화하며, 
* 인자로 전달된 `state`를 직접 수정할 수 있어 [State](??) 수정 관련 로직이 좀더 간결해 집니다.
* `state`를 수정하기 위해 복제본을 사용하지 않아도 됩니다.(*[개체/배열 State](https://tango1202.github.io/react/react-basic/#%EA%B0%9C%EC%B2%B4%EB%B0%B0%EC%97%B4-state) 참고*)

다음은 [combineReducers()](??)의 예를 [createSlice()](??)를 이용하여 리팩토링한 예입니다.

1. #1 : `createSlice()`로 이름, 초기값, 액션, 리듀서를 응집합니다.
2. #2 : 액션이 함수화 되어 있습니다. 이때 `PayloadAction`을 사용하며 `action.payload`에 실제 데이터가 전달됩니다.(*`payload`는 네트워크등 데이터 전송에서 사용하는 용어인데요, 전송에 필요한 부가적인 정보(헤더나 메타 데이터)를 제외한 실제 데이터 부분만을 `payload`라고 합니다.*)
3. #3 : 인자로 전달된 `state`를 직접 수정합니다. 
4. #4 : `configureStore()`에서 `combineReducers()`처럼 리듀서를 합칩니다.
5. #5 : `add()`, `setDirty()`등 함수화된 액션을 호출합니다. 이때 액션으로 전달될 `payload` 값을 전달합니다.

```tsx
import { useRef } from 'react';
import { Provider, useDispatch, useSelector } from 'react-redux';
import { configureStore, createSlice, PayloadAction } from '@reduxjs/toolkit';

// ----
// 사용자 이름을 관리하는 Slice 입니다.
// ----
interface INamesState {
  names: string[];
}

const namesInitialState: INamesState = {
  names: ['홍길동', '성춘향'],
};

// #1. createSlice()로 이름, 초기값, 액션, 리듀서를 응집합니다.
const namesSlice = createSlice({
  name: 'namesState',
  initialState: namesInitialState,
  // #2. 액션이 함수화 되어 있습니다.
  // 이때 PayloadAction을 사용합니다. action.payload에 실제 데이터가 전달됩니다.
  reducers: {
    add: (state: INamesState, action: PayloadAction<string>) => {
      // #3. 인자로 전달된 state를 직접 수정합니다.
      state.names.push(action.payload);
    },
    save: (state: INamesState, action: PayloadAction<string>) => {
      console.log('이름들을 저장합니다.');
    },
  },
});

// ----
// 데이터가 수정되었는지 여부를 관리하는 Slice 입니다.
// ----
interface IDirtyState {
  dirty: boolean;
}
const dirtyInitialState: IDirtyState = {
  dirty: false,
};

// #1. createSlice()로 이름, 초기값, 액션, 리듀서를 응집합니다.
const dirtySlice = createSlice({
  name: 'dirtyState',
  initialState: dirtyInitialState,
  reducers: {
    setDirty: (state: IDirtyState, action: PayloadAction<boolean>) => {
      state.dirty = action.payload;
    },
  },
});

// ----
// 리덕스에 저장되는 형태입니다.
// ----
interface IStore {
  namesState: INamesState;
  dirtyState: IDirtyState;
}

const MySlice = () => {
  // #4. configureStore()에서 combineReducers()처럼 리듀서를 합칩니다.
  const store = configureStore({
    reducer: {
      namesState: namesSlice.reducer,
      dirtyState: dirtySlice.reducer,
    },
  });

  return (
    <div>
      <Provider store={store}>
        <MyToolbar />
        <MyList />
      </Provider>
    </div>
  );
};

const MyToolbar = () => {
  const nameRef = useRef<HTMLInputElement>(null);
  const dispatch = useDispatch();

  const onAdd = () => {
    // #5. add(), setDirty()등 함수화된 액션을 호출합니다.
    // 이때 액션으로 전달될 payload 값을 전달합니다.
    const payload = nameRef.current ? nameRef.current.value : '';
    dispatch(namesSlice.actions.add(payload));
    dispatch(dirtySlice.actions.setDirty(true));
  };
  const onSave = () => {
    dispatch(namesSlice.actions.save(''));
    dispatch(dirtySlice.actions.setDirty(false));
  };
  return (
    <div>
      <span>{'(Slice 테스트) 이름'}</span>
      <input ref={nameRef} />
      <button onClick={onAdd}>{'추가'}</button>
      <button onClick={onSave}>{'저장'}</button>
    </div>
  );
};

const MyList = () => {
  const names = useSelector((store: IStore) => store.namesState.names);
  const dirty = useSelector((store: IStore) => store.dirtyState.dirty);

  return (
    <ol>
      {names.map((name) => (
        <li
          key={name}
          style={
            {
              backgroundColor: dirty ? 'red' : 'blue',
            }
          }
        >
          {name}
        </li>
      ))}
    </ol>
  );
};

export default MySlice;
```

# immer

리덕스나 [State](??)를 수정할때 복제본을 사용해야 하는데요(*[개체/배열 State](https://tango1202.github.io/react/react-basic/#%EA%B0%9C%EC%B2%B4%EB%B0%B0%EC%97%B4-state) 참고*), [createSlice()](??)를 사용하면, 인자로 전달된 `state`를 직접 수정해도 되므로, 코드를 좀더 직관적으로 작성할 수 있었습니다.

유사하게 [immer](??)를 사용할 수도 있습니다.

다음은 [combineReducers()](??)의 예를 [immer](??)를 이용하여 리팩토링한 예입니다.

1. #1 : 혹시 [immer](??)가 설치되지 않았다면, `npm install -D immer` 를 하면 됩니다.
2. #2 : `produce()`함수를 이용하여 `state`를 수정하는 함수를 사용합니다.

```tsx
import {produce, Draft} from 'immer'; // #1. 혹시 설치되지 않았다면, npm install -D immer 를 하면 됩니다.

const namesReducer = (state: INamesState = namesInitialState, action: INamesAction): INamesState => {
  // #2. produce() 를 이용하여 state를 수정하는 함수를 사용합니다.
  // 첫번째 인자 state : 수정하려는 개체
  // 두번째 인자 : state를 변경하는 함수
  return produce(state, (draft: Draft<INamesState>) => {
    switch (action.type) {
      case 'Add': 
        draft.names.push(action.name);
        break;
      case 'Save': 
        console.log('이름들을 저장합니다.');
        break;
      default:
        break;
    }
  });
};
```

# redux-actions

[combineReducers()](??)의 예를 보면 액션을 호출할때 다음과 같이 액션 개체를 생성후 `dipatch()`를 호출하는데요, 

```tsx
dispatch({
  type: 'Add',
  name: nameRef.current ? nameRef.current.value : '',
});
```

[redux-actions](??)을 사용하면 액션을 함수화하여 호출할 수 있으며, 좀더 구조적으로 카테고리화하여 액션을 관리할 수 있습니다. 다만 타입스크립트와는 궁합이 좀 안맞는 부분이 있어서, 좀 억지스럽게 `any`를 사용할 수도 있습니다.

터미널에서 다음 명령을 입력하여 `redux-actions`를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

```
npm install -D redux-actions @types/redux-actions
```

다음은 [immer](??)의 예를 [redux-actions](??)으로 적용한 예입니다.

1. #1 : `redux-actions`의 `Action`을 사용합니다.

    다음과 같이 `type`은 무조건 문자열로 되어 있고, `payload`라는 속성에 액션 데이터가 저장됩니다.

    ```tsx
    export interface BaseAction {
      type: string;
    }
    export interface Action<Payload> extends BaseAction {
      payload: Payload;
      error?: boolean | undefined;
    }
    ```

2. #2 : 액션 타입입니다. 아무 문자열이나 됩니다. 구조적 관리를 위해 카테고리/기능명의 형식으로 작성해 봤습니다.

3. #3 : `createAction()`을 이용하여 지정한 액션 타입으로 액션 함수를 만듭니다.

4. #4 : 각 액션 타입에 따른 액션 함수가 정의된 `reducerMap`을 정의합니다. 이전엔 `switch()` 로 분기했지만, `redux-actions`에서는 맵의 속성을 key로 하여 분기 합니다. 이때 #4-1과 같이 `Action`은 전달되는 데이터 타입으로 구체화해서 사용합니다.

5. #5 : `handleActions()`를 이용하여 `reducerMap`으로부터 `reducer`를 생성합니다.

6. #6 : `dispatch()`를 이용하여 액션을 호출합니다.다만, `Action`은 전달되는 데이터 타입으로 구체화해서 사용해야 합니다.

    `name`은 `string`이고, `dirty`는 `boolean`이므로 두가지 타입을 모두 허용했습니다. 다음처럼 `|`로 나열할 수도 있지만, 실무에선 보다 다양한 형식이 사용되기 때문에, 편의상 그냥 `any`를 사용하게 될 수도 있습니다.

```tsx
import { useRef } from 'react';
import { Provider, useDispatch, useSelector } from 'react-redux';
import { createAction, handleActions, Action } from 'redux-actions'; // #1. redux-actions의 Action을 사용합니다.
import { configureStore } from '@reduxjs/toolkit';
import { combineReducers, Dispatch } from 'redux';
import { produce, Draft } from 'immer'; 

interface INamesState {
  names: string[];
}

const namesInitialState: INamesState = {
  names: ['홍길동', '성춘향'],
};

// #2. 액션 타입입니다. 아무 문자열이나 됩니다. 구조적 관리를 위해 카테고리/기능명의 형식으로 작성해 봤습니다.
const ADD_USERS: string = 'users/ADD_USERS'; 
const SAVE_USERS: string = 'users/SAVE_USERS';

// #3. 지정한 액션 타입으로 액션 함수를 만듭니다. 
const addUsersAction = createAction(ADD_USERS);
const saveUsersAction = createAction(SAVE_USERS);

// #4. 각 액션 타입에 따른 액션 함수가 정의된 reducerMap을 정의합니다. 
// 이전엔 switch() 로 분기했지만, 맵의 속성을 key로 하여 분기 합니다.
const userActions = {
  // #4-1. Action은 전달되는 데이터 타입으로 구체화해서 사용합니다.
  [ADD_USERS]: (state: INamesState, action: Action<string>): INamesState => {
    return produce(state, (draft: Draft<INamesState>) => {
      console.dir(action);
      draft.names.push(action.payload);
    });
  },
  [SAVE_USERS]: (state: INamesState, action: Action<string>): INamesState => {
    return produce(state, (draft: Draft<INamesState>) => {
      console.log('이름들을 저장합니다.');
    });
  },
};

// #5. reducerMap으로부터 reducer를 생성합니다.
const namesReducer = handleActions(
  {
    ...userActions,
    // 여러 카테고리를 각각 관리하는 경우, Spread 문법을 이용하여 통합할 수 있습니다.
    // 예를 들면, 다음처럼요.
    // ...filesActions,
    // ...editActions,
    // ...viewActions,
    // ...helpActions
  },
  namesInitialState,
);

interface IDirtyState {
  dirty: boolean;
}
const dirtyInitialState: IDirtyState = {
  dirty: false,
};
const SET_DIRTY: string = 'doc/SET_DIRTY';
const setDirtyAction = createAction(SET_DIRTY);

const docActions = {
  [SET_DIRTY]: (state: IDirtyState, action: any): IDirtyState => {
    return produce(state, (draft: Draft<IDirtyState>) => {
      draft.dirty = action.payload;
    });
  },
};

const dirtyReducer = handleActions(
  {
    ...docActions,
  },
  dirtyInitialState,
);

interface IStore {
  namesState: INamesState;
  dirtyState: IDirtyState;
}

const MyReduxActions = () => {
  const rootReducer = combineReducers({
    namesState: namesReducer,
    dirtyState: dirtyReducer,
  });

  const store = configureStore({
    reducer: rootReducer,
  });

  return (
    <div>
      <Provider store={store}>
        <MyToolbar />
        <MyList />
      </Provider>
    </div>
  );
};

const MyToolbar = () => {
  const nameRef = useRef<HTMLInputElement>(null);
  // #6 : dispatch()를 이용하여 액션을 호출합니다.다만, Action은 전달되는 데이터 타입으로 구체화해서 사용해야 합니다.
  // 이름은 string이고, dirty는 boolean이므로 두가지 타입을 모두 허용했습니다. 
  // 다음처럼 '|'로 나열할 수도 있지만, 실무에선 보다 다양한 형식이 사용되기 때문에, 편의상 그냥 'any'를 사용하게 될 수도 있습니다.
  const dispatch = useDispatch<Dispatch<Action<string | boolean>>>();

  const onAdd = () => {
    dispatch(addUsersAction(nameRef.current ? nameRef.current.value : ''));
    dispatch(setDirtyAction(true));
  };
  const onSave = () => {
    dispatch(saveUsersAction());
    dispatch(setDirtyAction(false));
  };
  return (
    <div>
      <span>{'(redux-actions 테스트) 이름'}</span>
      <input ref={nameRef} />
      <button onClick={onAdd}>{'추가'}</button>
      <button onClick={onSave}>{'저장'}</button>
    </div>
  );
};

const MyList = () => {
  const names = useSelector((store: IStore) => store.namesState.names);
  const dirty = useSelector((store: IStore) => store.dirtyState.dirty);

  return (
    <ol>
      {names.map((name) => (
        <li
          key={name}
          style={{
            backgroundColor: dirty ? 'red' : 'blue',
          }}
        >
          {name}
        </li>
      ))}
    </ol>
  );
};

export default MyReduxActions;
```

# 리덕스 개발자 도구



