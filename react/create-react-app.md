# Create React App 관련 메모

## 2023. 02. 14.

[You need to put any JS and CSS files inside src, otherwise webpack won’t see them.](https://create-react-app.dev/docs/folder-structure#:~:text=You%20need%20to%20put%20any%20JS%20and%20CSS%20files%20inside%20src%2C%20otherwise%20webpack%20won%E2%80%99t%20see%20them.)

`npm start`에 관한 설명 중

- [The page will reload if you make edits. You will also see any lint errors in the console.](https://create-react-app.dev/docs/available-scripts#:~:text=The%20page%20will%20reload%20if%20you%20make%20edits.%20You%20will%20also%20see%20any%20lint%20errors%20in%20the%20console.)

> :memo: **lint:** [린트(lint) 또는 린터(linter)는 소스 코드를 분석하여 프로그램 오류, 버그, 스타일 오류, 의심스러운 구조체에 표시(flag)를 달아놓기 위한 도구들을 가리킨다.[1] 이 용어는 C 언어 소스 코드를 검사하는 유닉스 유틸리티에서 기원한다.](<https://ko.wikipedia.org/wiki/%EB%A6%B0%ED%8A%B8_(%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4)#cite_note-1:~:text=%EB%A6%B0%ED%8A%B8(lint)%20%EB%98%90%EB%8A%94%20%EB%A6%B0%ED%84%B0(linter)>)

'지원되는 브라우저 configuring'에 관한 설명 중

- package.json에서 `browserlist` 수정하면 제대로 반영되지 않을 수도 있는데, 이는 [babel-loader 이슈](https://github.com/babel/babel-loader/issues/690)로 인한 현상이다. 빠른 해결책으로는 `node_modules/.cache` 폴더를 삭제 후 다시 시도하는 방법이 있다.
  - 출처: ["Supported Browsers and Features"](https://create-react-app.dev/docs/supported-browsers-features#:~:text=When%20editing%20the%20browserslist%20config%2C%20you%20may%20notice%20that%20your%20changes%20don%27t%20get%20picked%20up%20right%20away.%20This%20is%20due%20to%20an%20issue%20in%20babel%2Dloader%20not%20detecting%20the%20change%20in%20your%20package.json.%20A%20quick%20solution%20is%20to%20delete%20the%20node_modules/.cache%20folder%20and%20try%20again.)

[ESLint 기본 구성 확장에 관한 설명 중](https://create-react-app.dev/docs/setting-up-your-editor#extending-or-replacing-the-default-eslint-config)

- TypeScript로 작업할 경우, TypeScript 파일만을 위한 룰을 `overrides`객체에 추가해야 한다.
