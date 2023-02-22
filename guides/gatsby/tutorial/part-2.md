# Part2: Use and Style React Component

> 이 글은 **Gatsbyjs**의 "[Part 2: Use and Style React Components](https://www.gatsbyjs.com/docs/tutorial/part-2/)"을 재가공한 것입니다. 원본 문서는 [MIT 라이선스](https://opensource.org/licenses/MIT)에 따라 사용할 수 있습니다.

<br>

<details><summary>목차</summary>
<p>

[Summary](#summary)

- [Key Takeaways](#key-takeaways)

</p>
</details>

[Part2: Use and Style React Component](#part2-use-and-style-react-component)

-

## Summary

복습용 질문

1. 페이지 컴포넌트와 building-block 컴포넌트의 차이점은?

   - 페이지 컴포넌트는 특정 페이지를 구성하는 모든 UI 엘리먼트를 포함하는 컴포넌트이다. 빌딩 블록 컴포넌트는 이 중에서 재사용이 가능한 것을 일컫는다.

2. 개츠비 사이트에 새로운 페이지를 추가하는 법은?

   - `src/pages` 경로에 .js 파일 추가. 파일 내 페이지 컴포넌트 생성 후 export

3. 페이지에 타이틀을 지정하는 법은?

   - 개츠비 Head API를 사용: `export const Head = () => <title>제목</title>`

4. 새 리액트 컴포넌트를 작성하기 위한 세 단계는?

   - 'react' 패키지로부터 리액트 import. .js 파일에서 JSX를 사용할 수 있다.
     &rarr; 컴포넌트 define. 이는 JSX 엘리먼트를 return하는 함수이다.
     &rarr; 컴포넌트 export.

5. props는 무엇이며, 이를 언제 사용하는가?

   - 컴포넌트로 전달하는 인자로, 페이지 렌더링 방식을 변경할 때 사용

6. `children` prop은 무엇이며 왜 유용한가?
   - 컴포넌트를 render할 때 시작 태그와 마침 태그 사이에 있는 컨텐츠면 무엇이든지 자동으로 전달되는데, 이것이 `children` prop이다. 일종의 프레임 역할을 하는 컴포넌트를 공통으로 사용하면서 안의 컨텐츠만 변경하고자 할 때 유용하다.

### Key takeaways

- React: UI를 component라 불리는 작은 조각으로 분해할 수 있게 도우는 라이브러리

  - Component: React element를 리턴하는 함수
  - React element는 JSX로 작성

- **페이지 컴포넌트**에는 특정 페이지를 구성하는 모든 UI element가 포함된다. 개츠비는 `src/pages` 경로의 파일에서 `default exports`하는 컴포넌트를 가지고 자동으로 페이지를 생성한다. 이 파일명은 페이지의 루트로 쓰인다.

- 페이지의 메타 데이터를 정의하는 기명함수 `Head`를 export해서**Gatsyby Head API** 사용할 수 있다.

**Building-block 컴포넌트**는 UI를 구성하는 컴포넌트 중 재사용 가능한 작은 단위이다. 페이지 컴포넌트 또는 다른 building-block 컴포넌트에서 이를 import할 수 있다.

- `Link`와 같이 다른 패키지로부터 **미리 빌드된 컴포넌트** import할 수 있다. 또는 `Layout`처럼 고유한 **커스텀** 컴포넌트를 만들 수도 있다.

- **props**로 컴포넌트 렌더링 방식을 바꿀 수 있다. 컴포넌트 빌드 시 고유한 props를 정의할 수 있다. 리액트는 `children`, `className`같은 built-in props 또한 가지고 있다.

- 개츠비에서는 기본적으로 **CSS Modules**로 style 작업을 한다.
