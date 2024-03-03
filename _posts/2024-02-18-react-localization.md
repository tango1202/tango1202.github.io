---
layout: single
title: "#6. [React] 다국어 문자열"
categories: "react"
tag: ["react"]
author_profile: false
sidebar: 
    nav: "docs"
---

# 개요

문자열 리소스를 코드내에 직접 작성하다보면, 일관된 관리가 힘들고 다국어 처리가 힘들어집니다.

리액트에서는 이러한 문자열 관리를 위해 [i18next(https://react.i18next.com/)](https://react.i18next.com/)을 사용합니다.

# i18next 설치

터미널에서 다음 명령을 입력하여 [i18next(https://react.i18next.com/)](https://react.i18next.com/)를 설치합니다.(*`-D`옵션을 주면 현 프로젝트에 설치되며, 생략하면 글로벌로 설치됩니다.*) 

```
npm install -D react-i18next i18next
```

# 다국어 문자열 파일 구성

1. `src/locales/kr/string.json` 에 번역할 한국어 파일을 작성합니다.

    이때 `file`과 같이 계층적으로 관리할 수도 있습니다.

    ```json
    {
      "idc_File": "파일",
      "idc_Edit": "편집",
      "file": { 
        "idc_Open": "열기",
        "idc_Save": "저장"
      }
    }
    ```

2. `src/locales/en/string.json` 에 영어 파일을 작성합니다.

    ```json
    {
      "idc_File": "File",
      "idc_Edit": "Edit",
      "file": {
        "idc_Open": "Open",
        "idc_Save": "Save"
      }
    }
    ```

3. `src/locales/i18n.ts` 파일을 다음과 같이 작성하여 #1과 같이 한국어일때는 `kr`, 영어일때는 `en`으로 관리합니다.

    ```typescript
    import i18n from 'i18next';
    import { initReactI18next } from 'react-i18next';
    import kr from './kr/string.json'; // 한국어 사전
    import en from './en/string.json'; // 영어 사전

    i18n.use(initReactI18next).init({
      resources: {
        en: { translation: en }, // #1. 영어 일때는 ./en/string.json 사용
        kr: { translation: kr }, // #1. 한국어 일때는 ./kr/string.json 사용
      },
      lng: 'kr', // 기본 언어는 kr을 사용합니다.
      fallbackLng: 'en', // 언어 사전이 없는 경우 en을 사용합니다.
      interpolation: { escapeValue: false }, // HTML 태그를 지원합니다.
    });

    export default i18n;
    ```
# 문자열 사용

다음은 문자열을 사용한 예입니다.

1. #1 : `useTranslation()`을 `import`합니다.
2. #2 : `src/locales/i18n.ts`에서 만든 `i18n` 개체를 `import` 합니다.

  만약 코드내에서 `i18n` 개체를 사용하지 않는다면, `import './locales/i18n';`와 같이 `import` 합니다.

3. #3: `useTranslation()`을 이용하여 `t()`함수를 구합니다. 향후 `t('idc_File')`와 같이 사용하여 설정된 언어에 맞게 문자열을 구할 수 있습니다.

4. #4 : `i18n.changeLanguage()`를 이용하여 강제로 언어 설정을 변경할 수 있습니다. 

```tsx
import { useState } from 'react';
import { useTranslation } from 'react-i18next'; // #1
import i18n from './locales/i18n'; // #2. 만약 i18n을 사용하지 않는다면 import './locales/i18n'; 로 import 합니다.

const MyString = () => {
  const { t } = useTranslation(); // #3
  const [isKr, setIsKr] = useState(true); // #4

  const onToggle = () => {
    i18n.changeLanguage(isKr ? 'en' : 'kr'); // #4. 한국어 이면 영어로, 영어이면 한국어로 바꿉니다.
    setIsKr(!isKr);
  };
  return (
    <>
      <button onClick={onToggle}>{`언어 토글`}</button>
      <div>{`${t('idc_File')}, ${t('file.idc_Open')}, ${t('file.idc_Save')}, ${t('idc_Edit')}`}</div>
    </>
  );
};
export default MyString;
```




