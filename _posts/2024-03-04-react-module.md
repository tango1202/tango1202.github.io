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

프로젝트 루트 폴더의 `tsconfig.json`으로 절대 경로를 사용할 수 있습니다. 다음과 같이 `baseUrl`과 `include`를 설정해 줍니다. 

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
import MyComponent1 from 'myModule/component/lib/MyComponent1';
import MyComponent2 from 'myModule/component/lib/MyComponent2';
import { IMyData } from 'myModule/component/lib/MyData';
```

# 다른 프로젝트의 외부 모듈

현 프로젝트 하위의 모듈이 아닌 다른 프로젝트의 모듈도 경로를 설정하여 사용할 수 있습니다. 공통 모듈을 별도의 프로젝트로 구성하고 이를 사용할때 유용합니다.

다음과 같이 `baseUrl`과 `paths`와 `include`를 설정해 줍니다. 이때 `otherModule`의 경로는 `baseUrl`의 상대 경로입니다.(*하기는 `../../otherModule`인 예입니다.*)

```json
{
  "compilerOptions": {
    "baseUrl": "src",
  },
  "paths": {
    "otherModule/*": ["../../otherModule/*"]
  },
  "include": [
    "src",
    "../../otherModule/**/*.ts", 
    "../../otherModule/**/*.tsx",
  ]
}
```

그러면 다음과 같이 `otherModule` 하위부터 절대 경로로 사용할 수 있습니다.

```tsx
import OtherComponent from 'otherModule/component/lib/OtherComponent';
```

혹시나 `import`시 `import/no-unresolved` 린트 오류가 발생하면, `.eslintrc.ts`의 `rules`에 다음과 같이 `amd: true`를 추가합니다.

```json
rules: {
  amd: true
}
```

# export용 index.ts 파일

`import` 경로 지정시 파일이 아닌 특정 폴더만 명시하면, 해당 폴더의 `index.ts` 파일에서 가져옵니다.

따라서 `myModule` 폴더에 `index.ts` 파일을 작성하고, `export`하려는 개체나 타입을 취합할 수 있습니다. 이렇게 다른 파일에 있는 개체를 다시 `export`하는 경우 `interface`나 `type`은 `type {}`을 사용해서 내보냅니다. 

```ts
// src/myModule/index.ts 에서
export {MyComponent1} from './comonent/lib/MyComponent1'; // 같은 모듈이므로 상대 경로로 했습니다.
export {MyComponent2} from './comonent/lib/MyComponent2';
export type { IMyData } from './comonent/lib/MyData'; // interface나 type은 type {} 을 사용합니다.
```

또한 `MyComponent1`, `MyComponent`가 모두 `export default`로 되어 있기 때문에 `index.ts`에서 모두 `default`로 내보낼 수 없습니다. 따라서 `default` 설정을 수정해야 합니다.

```tsx
// export default MyComponent1;
export {MyComponent1};

// export default MyComponent2;
export {MyComponent2};
```

`index.ts`에 취합하면, `App`에서 다음과 같이 한번에 `import`할 수 있어, 좀더 간결하게 사용할 수 있습니다.

```tsx
import {MyComponent1, MyComponent2, IMyData} from 'myModule';
```