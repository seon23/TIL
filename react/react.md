## 코딩애플님 리액트 기초 강의

리액트 데이터 바인딩
- 데이터 변수, 함수 등을 쉽게 바인딩할 수 있음
- className, src, id 등 속성에 { 변수명 또는 함수 등 }을 넣는다.
- 자바스크립트로 다 짜려면 코드가 매우 길어졌을 것.

JSX에서는
- Style prop value must be an object

리액트에서 변수 대신 state 쓰는 이유
- 앱처럼 동작하는 웹을 만들기 위해.
state로 만든 데이터가 바뀌면, 데이터를 담고 있는 html이 자동으로 다시 렌더링 된다. 그냥 변수 쓰면 새로고침 해야 바뀌는데 말이다. 스무스한 동작이 가능.
- 자주 바뀌는 데이터는 state에 저장해서 사용한다.

deep copy
- reference data type 찾아보기
- `let copy = [...arr];`
  - `let copy = arr;`는 복사가 아니라 값을 공유하는 거다.
- 리액트 원칙 중 하나가 immutable data이다.
  - state 데이터 직접 변경을 권장하지 않는다. 복사본 생성 후 얘를 변경해서 `state변경함수(변경한 복사본)`을 실행한다.

