---
layout: single
title: "#4. [React] 모듈 구성"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

컴포넌트의 갯수가 많아지면, 이를 응집하고 조직화하여 관리하는게 유지보수에 좋습니다.

다음은 `myModule`에 해당하는 `component`를 `src/myModule/component/lib`에 모아서 관리하는 예입니다.

```typescript
// src/myModule/component/lib 에서
export interface IContainsName {
  name:string;
}
```

```typescript
// src/myMoudle/component/lib 에서
import {IContainsName} from './ContainsName';

export interface IMyData extends IContainsName {
    addr: string;
};
```

```tsx
// src/myMoudle/component/lib 에서
const MyComponent1 = () => {
  return <div>{'MyComponent1입니다.'}</div>;
};
  
export default MyComponent1;
```

```tsx
// src/myMoudle/component/lib 에서
import {IMyData} from './MyData';

interface IProps {
  data:IMyData;
};
const MyComponent2 = (props:IProps) => {
  return <div>{`MyComponent2입니다. name = ${props.data.name}, addr = ${props.data.addr}`}</div>;
};

export default MyComponent2;
```

`src`폴더에 있는 `App`에서 이를 사용하려면 다음과 같이 `import` 해야 합니다.

```tsx
import MyComponent1 from './myModule/comonent/lib/MyComponent1';
import MyComponent2 from './myModule/comonent/lib/MyComponent2';
import {IMyData} from './myModule/comonent/lib/MyData';

function App() {
  const data:IMyData = {name:'홍길동', addr: '한국'};
  return (
    <>
      <IMyComponent/>
      <MyComponent2 data={data}/>
    </>
  );
}  
```     

`./`을 사용한 후 상대 경로를 이용해야 하고(*사용하려는 곳의 위치에 따라 `../../../../` 와 같은 경로 이동이 추가될 수 있습니다.*), 각 파일로 구분된 컴포넌트나 타입을 일일이 `import`해야 하기 때문에 불편합니다. 좀더 사용하기 쉽게 모듈을 구성하는게 좋습니다.

# 절대 경로

프로젝트 루트 폴더의 `tsconfig.json`으로 절대 경로를 사용할 수 있습니다. 다음과 같이 `baseUrl`을 설정해 줍니다.

```json
{
  "compilerOptions": {
    "baseUrl": "src",
  },
  "include": [
    "src" 
  ]
}
```

그러면 다음과 같이 `src` 하위부터 절대 경로로 사용할 수 있습니다.

```tsx
import MyComponent1 from 'myModule/comonent/lib/MyComponent1';
import MyComponent2 from 'myModule/comonent/lib/MyComponent2';
import { IMyData } from 'myModule/comonent/lib/MyData';
```

# export용 index.ts 파일

`import` 경로 지정시 파일이 아닌 특정 폴더만 명시하면, 해당 폴더의 `index.ts` 파일에서 가져옵니다.

따라서 `myModule` 폴더에 `index.ts` 파일을 작성하고, 내보내려는 개체나 타입을 취합하여 내보낼 수 있습니다. 이렇게 다른 파일에 있는 개체를 `export`하는 경우 `interface`나 `type`은 `type {}`을 사용해서 내보냅니다. 

```ts
// src/myModule/index.ts 에서
export {MyComponent1} from './comonent/lib/MyComponent1'; // 같은 모듈이므로 상대 경로로 했습니다.
export {MyComponent2} from './comonent/lib/MyComponent2';
export type { IMyData } from './comonent/lib/MyData'; // interface나 type은 type {} 을 사용합니다.
```

또한 `MyComponent1`, `MyComponent`가 모두 `export default`로 되어 있기 때문에 `index.ts`에서 모두 `default`로 내보낼 수 없습니다. 따라서 `export default MyComponent1;` 와 같이 `export` 한 개체를 `export {MyComponent1};` 와 같이 수정해야 합니다.

```tsx
// export default MyComponent1;
export {MyComponent1};

// export default MyComponent2;
export {MyComponent2};
```

그러면 `App`에서 다음과 같이 한번에 `import`할 수 있습니다.

```tsx
import {MyComponent1, MyComponent2, IMyData} from 'myModule';
```