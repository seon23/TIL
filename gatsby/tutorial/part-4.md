# Part 4: GraphQL로 데이터 쿼리

> 이 글은 **Gatsbyjs**의 "[Part 4: Query for Data with GraphQL](https://www.gatsbyjs.com/docs/tutorial/part-4/)"을 재가공한 것입니다. 원본 문서는 [MIT 라이선스](https://opensource.org/licenses/MIT)에 따라 사용할 수 있습니다.

<br>

<details><summary>목차</summary>
<p>

</p>
</details>

[소개](#소개)
[Gatsby의 GraphQL data layer](#gatsby의-graphql-data-layer)
[GrpahiQL 사용해서 데이터 레이어 탐색, GraphQL 쿼리 작성하기](#grpahiql-사용해서-데이터-레이어-탐색-graphql-쿼리-작성하기)
[building block 컴포넌트에서 쿼리 사용](#building-block-컴포넌트에서-쿼리-사용)

- [Task: GrpahiQL 사용해서 쿼리 빌드하기](#task-grpahiql-사용해서-쿼리-빌드하기)
- [Task: useStaticQUery 사용해서 사이트의 title을 Layout 컴포넌트로 pull하기](#task-usestaticquery-사용해서-사이트의-title을-layout-컴포넌트로-pull하기)
- [Task: `useStaticQuery` 사용해서 Seo 컴포넌트 생성하기](#task-usestaticquery-사용해서-seo-컴포넌트-생성하기)

## 소개

앞에선 리액트 컴포넌트를 통해 바로 텍스트와 이미지를 추가함. 웹사이트 빌드하기 좋은 방법이지만, 데이터를 생성하고 유지하는 더 쉬운 방법이 있음

- 마크다운 파일을 담은 폴더라든지
- content management system(CMS) 등

> Gatsby's data layer: powered by **GraphQL**  
> GraphQL: a query language

How to add data to Gatsby's data layer  
How to pull that data into React components

**data layer**

- pull data into your site from anywhere
- combine data from multiple sources, which lets you choose the best platform for each type of data.

**GraphQL**

- query language
- provide data needed inside component

`useStaticQUery` hook

- pull data into "building-block" component

`Seo`comoponents

## Gatsby의 GraphQL data layer

일단 내 데이터는 하나 이상의 *source*에 저장됨. 이 소스는 컴퓨터 파일시스템의 폴더일 수도, 워드프레스나 데이터베이스같은 CMS(content management system)일 수도 있음.

어떻게 소스로부터 데이터를 겟해서 데이터 레이어로 넘기냐?

- **source plugin**을 사이트에 추가한다.
  - 각 소스 플러그인은 특정 소스와 통신하도록 설계됨
- 그니까 내가 사이트를 빌드하면, 각 소스 플러그인이 특정 소스로부터 데이터를 pull해서 내 사이트의 GraphQL data layer에 추가하는 거지.

data layer에서 데이터를 다시 받는 방법은?

- **GraphQL query**. 얘를 컴포넌트로 들여와 사이트 내 필요한 곳에 데이터를 pull 하면 된다.
- 사이트 빌드 시, 개츠비는 컴포넌트 내 모든 GraphQL 쿼리를 찾을 거고, 걔네를 실행해서 컴포넌트에 결과 데이터를 줄 거다.

![A diagram showing how data flows into and out of the GrapQL data layer.](https://www.gatsbyjs.com/static/e45422900475b86807bc002fb6863b85/10d53/data-layer.png)
[_그림 출처 - Gatsby_](https://www.gatsbyjs.com/docs/tutorial/part-4/)

다시 말하면 source plugin이 데이터 소스(데이터가 보관된 곳이라고 생각하면 될 듯)에서 사이트의 GraphQL 데이터 레이어로 데이터를 넘기면 여기 있는 데이터를 GraphQL 쿼리가 리액트 컴포넌트로 보내서, 적절한 데이터를 사이트에 보여주는 건가?

## GrpahiQL 사용해서 데이터 레이어 탐색, GraphQL 쿼리 작성하기

## building-block 컴포넌트에서 쿼리 사용

### Task: GrpahiQL 사용해서 쿼리 빌드하기

### [Task: useStaticQUery 사용해서 사이트의 title을 Layout 컴포넌트로 pull하기](https://www.gatsbyjs.com/docs/tutorial/part-4/#task-use-usestaticquery-to-pull-the-site-title-into-the-layout-component)

> :memo: **Note:** `useStaticQuery`는 파일 당 한 번만 호출할 수 있다. 여러 필드를 쓸 거라면 쿼리 하나에 다 추가해라. 예를 들어 `site` 필드와 `siteBuildMetadata` 필드의 데이터가 필요하다면 아래와 같은 형태로 `useStaticQuery`를 호출해라

```js
const data = useStaticQuery(graphql`
  query {
    site {
      siteMetadata {
        title
      }
    }
    siteBuildMetadata {
      buildTime
    }
  }
`);
```

### [Task: `useStaticQuery` 사용해서 Seo 컴포넌트 생성하기](https://www.gatsbyjs.com/docs/tutorial/part-4/#task-use-usestaticquery-to-create-an-seo-component)

> :bulb: **Pro Tip:** `useStaticQuery`는 작고 재사용 가능한 리액트 컴포넌트 생성에 매우 적합하다. 아래 예시처럼 React hook을 직접 만들 수도 있다.

```js
import * as React from "react";
import {graphql, useStaticQuery} from "gatsby";

const useSiteMetadata = () => {
  const data = useStaticQuery(graphql`
    query {
      site {
        siteMetadata {
          title
        }
      }
    }
  `);

  return data.site.siteMetadata;
};

export default useSiteMetadata;
```

## [페이지 컴포넌트에서 쿼리 사용: 게시물의 파일명 리스트가 보이는 블로그 페이지 생성하기](https://www.gatsbyjs.com/docs/tutorial/part-4/#queries-in-page-components-create-a-blog-page-with-a-list-of-post-filenames)

### Task: 새로운 블로그 페이지 생성하기

### Task: MDX로 작성한 블로그 게시물 생성하기

### Task: GrpahiQl 사용해서 쿼리 빌드하기

### Taks: 페이지 쿼리 사용해서 게시물의 파일명 리스트를 블로그 페이지로 pull하기

## Summary

### Key takeaways

### 다음에 배울 내용
