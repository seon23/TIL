# 개츠비 공식 문서 튜토리얼
작성 시작: 2023-02-09  
gatsby 폴더 내의 모든 문서는 다음의 라이센스를 따릅니다.
(여기에 개츠비가 게시한 라이센스 사본)

mdn 폴더 내의 모든 문서는 다음의 라이센스를 따릅니다.

그냥 이런 식으로 LICENSE.md에 게시해도 될 것 같다.


일단 guides/gatsby 폴더의 저작권 표시는 하되, 
<details><summary>목차</summary>
<p>


</p>
</details>

[Part2: Use and Style React Component](#part2-use-and-style-react-component)  
- [Summary](#summary)
    - [Key Takeaways](#key-takeaways)

[Part 3: Add Features with Plugins](#part-3-add-features-with-plugins)
- [plugin이란?](#plugin이란)
- [사이트에 plugin 추가](#사이트에-plugin-추가)
- [Task: 홈페이지에 static image 넣기(`gastby-plugin-image`)](#task-gastby-plugin-image를-사용해-홈페이지에-static-image-넣기)

[Part 4: Query for Data with GraphQL](#part-4-query-for-data-with-graphql)
- [Introducton](#introduction)
- [Gatsby의 GraphQL data layer](#gatsby의-graphql-data-layer)



## Part2: Use and Style React Component

### Summary
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


#### Key takeaways
- React: UI를 component라 불리는 작은 조각으로 분해할 수 있게 도우는 라이브러리
    - Component: React element를 리턴하는 함수
    - React element는 JSX로 작성

- **페이지 컴포넌트**에는 특정 페이지를 구성하는 모든 UI element가 포함된다. 개츠비는 `src/pages` 경로의 파일에서 `default exports`하는 컴포넌트를 가지고 자동으로 페이지를 생성한다. 이 파일명은 페이지의 루트로 쓰인다.

- 페이지의 메타 데이터를 정의하는 기명함수 `Head`를 export해서**Gatsyby Head API** 사용할 수 있다.

**Building-block 컴포넌트**는 UI를 구성하는 컴포넌트 중 재사용 가능한 작은 단위이다. 페이지 컴포넌트 또는 다른 building-block 컴포넌트에서 이를 import할 수 있다.

- `Link`와 같이 다른 패키지로부터 **미리 빌드된 컴포넌트** import할 수 있다. 또는 `Layout`처럼 고유한 **커스텀** 컴포넌트를 만들 수도 있다.

- **props**로 컴포넌트 렌더링 방식을 바꿀 수 있다. 컴포넌트 빌드 시 고유한 props를 정의할 수 있다. 리액트는 `children`, `className`같은 built-in props 또한 가지고 있다.

- 개츠비에서는 기본적으로 **CSS Modules**로 style 작업을 한다.

---

## Part 3: Add Features with Plugins

### plugin이란?
개츠비에서 **plugin**은 사이트 부가 기능을 추가하기 위해 설치하는 npm 패키지를 말한다.

### 사이트에 plugin 추가
사이트에 plugin을 추가하려면 다음의 과정이 필요하다.
<details><summary>1. <b>Install the plugin</b> using npm.</summary>
<p>

아래 명령으로 plugin이 `package.json`파일과 `package-lock.json` 파일에 dependency로 추가된다.  
```shell
npm install plugin-name
```

![dependency meaning](../images/dependency%20meaning.jpg)
- dependency는 의존하는 기능, 필요로 하는 기능이라고 개인적으로 해석 중이다.

어떤 플러그인을 사용하는지에 따라 설치해야할 dependency가 더 많을 수도 있다.

</p>
</details>

<details><summary>2. <b>Configure the plugin</b> in your site’s gatsby-config.js file.</summary>
<p>

`gatsby-config.js` 파일에는 플러그인 configuration을 포함한 사이트 정보가 담겨있다. `plugins` 배열에 플러그인을 추가할 수 있다.

```js
module.exports = {
  siteMetadata: {
    title: "My First Gatsby Site",
  },
  plugins: ["plugin-name"], //배열 안에 스트링 형태가 들어 있음
};
```

어떤 플러그인은 부가적인 configuration 옵션을 필요로 한다. 이 경우에는 `plugins` 배열에 <u>string 대신 object</u>를 넣는다. 

```js
module.exports = {
  siteMetadata: {
    title: "My First Gatsby Site",
  },
  plugins: [
    // 배열 안에 스트링 대신 객체가 들어 있음
    {
      resolve: "plugin-name",
      options: {
        // 필요한 옵션은 플러그인 README에서 확인
      }
    },
  ]
};
```

> :memo: **Note:** `gatsby-config.js` 파일 업데이트가 반영되도록 다시 `gatsby develop`을 명령한다. 

</p>
</details>

3. **Use the plugin** features in your site, as needed.

### Task: `gastby-plugin-image`를 사용해 홈페이지에 static image 넣기
해당 플러그인은 다른 플러그인을 필요로 하는데, 관련 설명은 [여기](https://www.gatsbyjs.com/docs/tutorial/part-3/#introduction:~:text=The%20StaticImage%20component%20requires,install%20it%20for%20now.)에서 확인 가능하다.

---
