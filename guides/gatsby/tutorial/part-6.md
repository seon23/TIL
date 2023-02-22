# Part 6: Create Pages Programmtically

> 이 글은 **Gatsbyjs**의 "[Part 6: Create Pages Programmtically](https://www.gatsbyjs.com/docs/tutorial/part-6/)"을 재가공한 것입니다. 원본 문서는 [MIT 라이선스](https://opensource.org/licenses/MIT)에 따라 사용할 수 있습니다.

<br>

<details><summary>목차</summary>
<p>

[소개](#소개)

[개츠비 File System Route API를 사용해서 새로운 route를 동적으로 생성하기](#개츠비-file-system-route-api를-사용해서-새로운-route를-동적으로-생성하기)

- [Task: 블로그 게시물 페이지 템플릿 생성하기](#task-블로그-게시물-페이지-템플릿-생성하기)
- [Task: `/blog` path 파라미터로 경로 업데이트하기](#task-blog-path-파라미터로-경로-업데이트하기)

[블로그 게시물 페이지 템플릿에 컨텐츠 렌더링하기](#블로그-게시물-페이지-템플릿에-컨텐츠-렌더링하기)

[블로그 페이지 업데이트: 게시물 링크 삽입](#블로그-페이지-업데이트-게시물-링크-삽입)

[요약](#요약)

- [핵심 내용](#핵심-내용)
- [다음에 배울 내용](#다음에-배울-내용)

</p>
</details>

## 소개

Part5에서는 모든 블로그 게시물을 Blog 페이지에 추가했었다. 이는 사이트에 게시물을 추가하면 할 수록 Blog 페이지 길이가 점점 길어진다는 것을 의미한다. 게시물마다 자체 페이지가 있다면, Blog 페이지에 각기 다른 게시물로 연결되는 링크를 넣을 수 있다.

지금껏 개츠비 사이트에서 새로운 페이지를 생성했던 방법은 `src/pages` 폴더에 새로운 파일을 만들어서 JSX에서 페이지의 내용을 하드 코딩하는 방식이다. 하지만 매뉴얼대로 각 게시물마다 새로운 페이지를 생성하는 건 꽤나 반복적인 일이다. 특히 페이지가 같은 구조를 공유하기 때문에 그렇다. (frontmatter와 MDX 파일의 컨텐츠를 렌더링하는 방식)

이번 장에서는 개츠비가 제공하는 Filesystem Route API를 사용해서 새로운 페이지를 동적으로 생성하는 법을 배울 것이다.

이 장을 마칠 때면 다음의 작업이 가능해진다.

- 개츠비가 제공하는 Filesystem Route API를 사용해서 각 게시물마다 새로운 페이지를 동적으로 생성한다.
- 게시물 내용을 렌더링한다.
- 페이지 쿼리에 쿼리 변수를 추가한다.

## 개츠비 File System Route API를 사용해서 새로운 route를 동적으로 생성하기

개츠비 사이트를 빌드할 때, 개츠비는 `src/pages` 디렉토리에 있는 각 페이지 컴포넌트마다 새로운 route를 생성한다. 지금까지 파일 하나 당 한 페이지를 빌드했었다. (`index.js` 파일은 Home 페이지를, `about.js` 파일은 About 페이지를, `blog.js` 파일은 Blog 페이지를 생성)

그런데 페이지 컴포넌트 하나로 여러 페이지를 생성할 수도 있다. 모든 컨텐츠를 하드 코딩으로 만드는 대신, 페이지의 기본 구조를 잡는 템플릿 하나를 생성하면 개츠비가 빌드 타임에 각 페이지에 맞는 특정 데이터에 추가할 수 있다. 이런 작업을 위해, 개츠비 **File System Route API**를 사용한다. 이는 특수한 구문으로 작성된 페이지 파일에 이름을 부여함으로써 route를 동적으로 생성하게 한다.

- Key Gatsby Concept: File System Route API
  개츠비의 [File System Route API](https://www.gatsbyjs.com/docs/reference/routing/file-system-route-api/)는 `src/pages` 디렉토리에 있는 파일 이름을 짓는 특수한 문법을 정의한다. 이 작업을 통해, 데이터 레이어에 있는 노드 **collection**에 기반해서 페이지를 동적으로 생성할 수 있다.
  예를 들어, 사이트가 데이터 레이어에 많은 `Product` 노드를 가지고 있다고 상상해 보자. File System Route API를 사용해서 product 페이지 템플릿 컴포넌트를 하나 생성할 수 있을 것이다. 그렇게 사이트를 빌드했을 때, 개츠비는 페이지 템플릿과 각 `Product` 노드에 해당하는 데이터를 결합해서 product 마다 새로운 페이지를 생성할 것이다. product 페이지를 바꾸고 싶을 때는 템플릿 컴포넌트만 수정하면 된다. 그러면 개츠비가 사이트를 다시 빌드할 때 product 페이지를 업데이트할 것이다.
  collection route를 만드려면,
  1. 어떤 노드 타입으로부터 페이지를 생성할 지를 결정한다.
  2. 그 노드에 있는 어떤 필드를 페이지의 route(URL)에 사용할 지를 선택한다.
  3. `src/pages` 디렉토리에 새 페이지를 생성한다. 네이밍 컨벤션은 `{nodeType.field}.js`이다.
     - 파일명에 중괄호 (`{}`)를 반드시 포함한다. 이는 route의 동적 파트임을 나타낸다.
       예를 들어, `Product` 노드마다 페이지를 분리해서 생성하고 싶다면, 제품의 `name` 필드를 URL에 사용하고 싶다면, `src/pages/{Product.name}.js`에 새 파일을 생성하면 된다. 그러면 개츠비가 `/water-bottle/`, `/sweatshirt/`, `/notebook/`처럼 route에 해당 페이지를 생성할 것이다.

이번 장에서는 개츠비의 FIle System Route API를 사용해서 각 게시물에 대한 새 페이지를 동적으로 생성한다.

API에 따르면 collection route를 생성하기 이전에 두 가지 결정을 할 필요가 있다.

- 어떤 node *type*으로부터 페이지를 생성할 지
- 노드 타입의 어떤 *field*를 URL에 사용할 지

블로그 게시물을 MDX로 작성했기 때문에 MDX를 페이지를 생성하는 노드 타입으로 사용한다. 그러면 MDX에서 어떤 필드를 사용해야 하나?

이미 frontmatter에 필요한 정보를 추가했다. 지난 장에서 생성했던 `.mdx` 파일에 frontmatter에 `slug` 필드가 있었다. 다음 쿼리를 실행하면,

```graphql
{
  allMdx {
    nodes {
      frontmatter {
        slug
      }
    }
  }
}
```

다음과 같은 결과가 나온다.

```json
{
  "data": {
    "allMdx": {
      "nodes": [
        {
          "frontmatter": {
            "slug": "my-first-post"
          }
        },
        {
          "frontmatter": {
            "slug": "yet-another-post"
          }
        },
        {
          "frontmatter": {
            "slug": "another-post"
          }
        }
      ]
    }
  },
  "extensions": {}
}
```

URL에 적합한 좋은 포맷이다.

> 📝 **Note:** 이런 \*\*\*\*경우에, `slug` 필드는 좋은 선택이다. 사람이 읽기 쉬워서 게시물의 URL을 사용자가 이해하기 쉽기 때문이다. 물론 route에서 어떤 필드든 사용할 수 있다. 특수 문자나 공백이 포함되어 있어도 상관없다. File System Route API를 사용하면 개츠비는 모든 route를 "slugify"한다. 예를 들어, `I ♥ Dogs`이 `i-love-dogs`로 바뀐다.

## Task: 블로그 게시물 페이지 템플릿 생성하기

사용할 노드 타입과 필드를 정했으니 이를 File System Routes 네이밍 컨벤션으로 plug할 수 있다. MDX 노드의 `slug` 필드로부터 새 페이지를 생성하기 위해, `src/pages/{mdx.frontmatter__slug}.js`에 새 파일을 만든다.

아래 다이어그램은 개츠비가 사이트를 빌드할 때 생성할 route를 설명한다.

![A diagram showing how files in the "src/pages" directory get turned into routes for the site. Extended description below.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3c15dc4b-6a1f-498d-9688-aaf1309d1d5b/Untitled.png)

A diagram showing how files in the "src/pages" directory get turned into routes for the site. Extended description below.

- _자세한 설명_
  사이트를 빌드할 때, 개츠비는 `src/pages` 디렉토리의 페이지 컴포넌트를 검토하고 사이트에 대한 새 페이지를 생성한다.
  - `src/pages/index.js`는 `/` 경로에 있다.
  - `src/pages/blog.js`는 `/blog/` 경로에 있다.
  - `src/pages/{mdx.frontmatter__slug}.js`는 다중 경로로 바뀐다. (데이터 레이어의 각 MDX 노드에 맞는)
    - 개츠비는 MDX 노드와 `my-first-post` slug를 사용해서 `/my-first-post/` 경로에 페이지를 빌드한다.
    - 개츠비는 MDX 노드와 `anoter-post` slug를 사용해서 `another-post` 경로에 페이지를 빌드한다.
    - 개츠비는 MDX 노드와 `yet-anoter-post` slug를 사용해서 `yet-another-post/` 경로에 페이지를 빌드한다.

1. `src/pages` 디렉토리에 `{mdx.frontmatter__slug}.js`라는 이름으로 새 파일을 생성한다. 블로그 게시물 페이지 템플릿이 되는 파일이다.
2. `{mdx.frontmatter__slug}.js`파일에 기본 페이지 컴포넌트를 만든다. 일단은 `Layout` 컴포넌트를 추가하는데, 페이지 타이틀과 컨텐츠를 하드 코딩으로 만든다. (후에 다이나믹으로 만들 거다.)

```jsx
import * as React from "react";
import Layout from "../components/layout";
import Seo from "../components/seo";

const BlogPost = () => {
  return (
    <Layout pageTitle='Super Cool Blog Posts'>
      <p>My blog post contents will go here (eventually).</p>
    </Layout>
  );
};

export const Head = () => <Seo title='Super Cool Blog Posts' />;

export default BlogPost;
```

1. 웹 브라우저에서 `localhost:8000/my-first-post/`을 방문하면 하드 코딩으로 만든 컨텐츠가 보일 것이다. 동일한 페이지가 게시물에 맞게 생성됐는지 확인하기 위해 URL은 다른 게시물의 slug로 업데이트할 수 있다. 다른 게시물의 slug로 URL을 변경해서 각 게시물에 맞는 페이지가 생성되었는지 확인할 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6d450300-08db-4097-b3bc-d622d0830757/Untitled.png)

> **Pro Tip:** 어떤 페이지가 생성되지 않았는지 확실하지 않나? `gatsby develop`을 실행했을 때 404 페이지가 나와는지 확인해 봐라. (존재하지 않는 페이지 URL에 접근했을 때 404 페이지가 나타난다.) 페이지 하단에 개츠비가 사이트에 대해새 생성한 모든 페이지의 경로 목록이 있다.
>
> ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00162a8b-9b8d-4ef1-a0f9-76577ea3220a/Untitled.png)

## Task: `/blog` path 파라미터로 경로 업데이트하기

앞에서 모든 페이지를 루트 도메인에서부터 생성했다. (`localhost:8000/my-first-post`) 그러나 모든 게시물을 `/blog` path 파라미터 아래로 그룹화하면 검색 엔진 최적화(SEO)나 전반적인 구조 면에서 더 좋아질 것이다.

개츠비가 폴더 구조에 기반하여 `src/pages` 디렉토리에서 페이지 route를 빌드했기 때문에, `src/pages` 내부에 서브 디렉토리를 생성해서 새로운 path 파라미터를 페이지에 추가할 수 있다.

아래 다이어그램은 게시물의 route에 `blog` path 파라미터를 추가하기 위해 수정해야할 내용의 개요를 보여준다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b412a2e3-f15f-40c7-bb6d-6f6b806d71a2/Untitled.png)

1. `src/pages` 디렉토리에 `blog` 폴더를 새로 생성한다.
2. `src/pages/{mdx.frontmatter__slug}.js`를 새로운 `blog` 폴더로 옮긴다. `Layout`과 `Seo` 컴포넌트 import 부분을 새 폴더 구조를 반영해서 변경한다.

```jsx
import * as React from "react";
import Layout from "../../components/layout";
import Seo from "../../components/seo";

// ...
```

1. 로컬 개발 서버가 사이트를 다시 빌드하면 게시물에 대한 path가 업데이트되었는 지를 웹 브라우저에서 확인한다.
   - 예를 들어, 현재 `[localhost:8000/blog/my-first-post/`에](http://localhost:8000/blog/my-first-post/에) 페이지가 있어야 하며, `blog` path 파라미터 없이 `[localhost:8000/my-first-pages/`에](http://localhost:8000/my-first-pages/에) 접근하면 404 페이지가 나타나야 한다.

> **Pro Tip:** 개츠비는 사이트를 빌드할 때 이후 빌드 작업을 더 빠르게 하기 위해 사이트에 대한 정보를 캐싱한다. 하지만 사이트를 변경할 때, 이 작업에 대한 캐시를 비워야할 경우도 있다.

예상하지 않은 결과를 마주했을 때, (로컬 개발 서버가 변경한 부분을 반영하지 않은 경우와 같이) `gatsby clean`을 커맨드 라인에서 실행하여 캐시를 삭제하면 다음에 빌드할 때 깨끗한 상태로 시작할 수 있다.

개츠비 CLI가 전역으로 설치되지 않았다면 `npx gatsby clean`을 대신 실행한다.

>

1. 구조화를 위해, 블로그와 관련된 모든 페이지를 한 곳에 모으는 것이 좋겠다. `src/pages/blog.js` 파일을 `srt/pages/blog` 디렉토리로 옮긴다.

   - `blog.js`를 `index.js`로 수정하고, 새로운 폴더 구조를 반영해서 `Layout` 컴포넌트를 import하는 부분도 업데이트한다.

   ```jsx
   import * as React from "react";
   import {graphql} from "gatsby";
   import Layout from "../../components/layout";

   // ...
   ```

   - 변경한 부분이 반영되도록 로컬 개발 서버를 중단한 후 다시 시작한다.

2. 웹 브라우저에서 Blog 페이지가 여전히 `[localhost:8000/blog/](http://localhost:8000/blog/)` 에 보이는지 확인한다.

## 블로그 게시물 페이지 템플릿에 컨텐츠 렌더링하기

이제 게시물에 대한 모든 페이지가 준비되었다. 실제 게시물 컨텐츠에 pull할 때이다. [Part4](https://www.gatsbyjs.com/docs/tutorial/part-4/)에서 GraphQL 쿼리를 사용하여 데이터를 컴포넌트로 pull하는 법을 배웠다. 그런데, 데이터 레이어로부터 어떤 MDX 노드를 각 페이지에 pull할 건지를 개츠비에 어떻게 전달할 수 있을까? **쿼리 변수** 개념이 여기서 나온다.

- Key GraphQL Concept: Query Varaibles
  GrpahQl에서 쿼리변수는 내 요청에 따라 추가 데이터를 보내주는 방법이다. 쿼리변수를 가지고, 내가 전달하는 변수에 기반하여 각기 다른 데이터를 반환하는 동적 쿼리를 작성할 수 있다.
  GraphiQL 예제를 살펴보자. [Part 5](https://www.gatsbyjs.com/docs/tutorial/part-5/)에서 응답 결과로 받는 데이터를 변경하기 위해 필드에 인자를 전달하는 법을 배웠다.
  아래 쿼리를 사용해서 값이 `"anoter-post"`와 동일한 `slug` 필드를 갖는 MDX 노드 데이터를 요청하면,
  ```graphql
  query MyQuery {
    mdx(frontmatter: {slug: {eq: "another-post"}}) {
      frontmatter {
        title
      }
    }
  }
  ```
  다음과 같은 응답을 반환한다.
  ```json
  {
    "data": {
      "mdx": {
        "frontmatter": {
          "title": "Another Post"
        }
      }
    },
    "extensions": {}
  }
  ```
  이 경우에 `slug` 값이 GrpahQL 쿼리로 하드 코딩된다. 그런데 페이지마다 다른 값을 가지도록 바꾸고 싶다면 어떻게 해야할까? 쿼리 변수가 여기서 나온다.
  GraphiQL의 쿼리 편집기 창 하단에 목록을 접었다 펼칠 수 있는 “Query Variables” 섹션이 있다. 이것을 클릭하면 새 텍스트 창이 나오는데, 쿼리로 전달하고 싶은 데이터에 대한 key-vaelu 쌍을 여기에 추가할 수 있다. 참고로 key-value 쌍은 JSON으로 작성해야 한다. 예를 들어, Query Variables 섹션에 객체를 추가하면 받을 응답에 `another-post`값을 갖는 `slug` 변수가 전달된다.
  ```json
  {
    "slug": "another-post"
  }
  ```
  쿼리에 쿼리 변수를 사용하기 위해서 다음 작업이 필요하다.
  1. **쿼리 변수 정의.** 앞에 `$`이 붙은 변수명과, GrpahQL 데이터 타입이 포함되어야 한다.
  2. **쿼리 변수를 쿼리에 사용.** (마찬가지로 변수명 앞에 `$`을 붙여야 한다.)
     다음은 필드 인자 대신 쿼리 변수를 사용하기 위해 이전 쿼리를 업데이트하는 예시이다.
  ```graphql
  query MyQuery($slug: String) {
    mdx(frontmatter: {slug: {eq: $slug}}) {
      frontmatter {
        title
      }
    }
  }
  ```
  위의 새로운 쿼리를 실행하면 쿼리 변수가 없었던 이전과 동일한 응답을 반환한다. 하지만 이제는 `slug` 변수 값을 `"my-first-post"`같은 다른 값으로 교체할 수 있으며 response는 알맞은 노드를 다시 보낼 것이다.
  아래 다이어그램은 쿼리와 쿼리 변수, response가 GrpahiQL 인터페이스에서 어떻게 어울리는지를 보여준다.
  ![https://www.gatsbyjs.com/static/5ff722d1302b010eadc61f36eedb4122/321ea/graphiql-with-query-variables.png](https://www.gatsbyjs.com/static/5ff722d1302b010eadc61f36eedb4122/321ea/graphiql-with-query-variables.png)
  > **Note:** 개츠비에서 쿼리 변수는 페이지 쿼리 안에서만 사용할 수 있다. (useStaticQuery hook에서는 사용할 수 없다.)

개츠비의 File Systme Route API를 사용하면, 일부 props를 각 페이지에 대한 페이지 템플릿 컴포넌트에 자동으로 추가한다.

- 페이지를 생성할 때 사용하는 데이터 레이어 노드의 `id`
- route의 동적 파트를 생성할 때 사용했던 필드 (현재로서는 `frontmatter_slug` 필드)

내부 동작을 보면, 개츠비는 두 값을 페이지 쿼리 안에서 쿼리 변수로 사용할 수 있도록 한다.

> **더 자세한 설명**

1. `BlogPost` 페이지 컴포넌트의 `props`를  `src/pages/blog/{mdx.frontmatter__slug}.js`에 출력하기 위해`console.log`문을 추가한다.
2. `localhost:8000/blog/my-first-post/` 에서 개발자 도구를 열면 콘솔 창에서 아래와 유사한 객체를 볼 수 있다.

> ```jsx
> Object {
>   // ...
>   pageContext: Object {
>     id: "11b3a825-30c5-551d-a713-dd748e7d554a"
>     frontmatter__slug: "my-first-post"
>   }
>   // ...
> }
> ```
>
> `pageContext` 객체의 key(`id`, `frontmatte__sulg`)는 File System Route API를 생성할 때 추가된다. 해당 key는 블로그 게시물 페이지 템플릿에서 쿼리 변수로 사용할 수 있다.

1. 먼저, GraphiQL을 사용해서 블로그 게시물 페이지 템플릿에 대한 페이지 쿼리를 생성한다.
   - 각 페이지는 MDX 노드 하나의 데이터만 필요로 하므로, `mdx` 필드를 사용한다. (앞에서는 `allMdx`를 사용했었다.)
   - 노드를 찾는 가장 빠른 방법은 `id` 필드를 사용하는 것이다. 쿼리 변수인 `id`를 `frontmatter__slug` 대신 사용하자.

```graphql
query MyQuery($id: String) {
  mdx(id: {eq: $id}) {
    frontmatter {
      title
      date(formatString: "MMMM D, YYYY")
    }
  }
}
```

> **Tip:** GrpahiQL에서 쿼리를 검사하고 싶다면(쿼리가 맞게 작성됐는지?) `id` 키를 Query Variables 섹션에 추가한다. GraphiQL에서 `allMdx`쿼리를 실행함으로써 `id` 값 중 하나를 복사할 수 있다.

Query Variables 섹션에 입력하는 JSON 객체는 아래와 같은 형태여야 한다. (참고로 아래 코드를 복사하면 제대로 작동하지 않을 테니 실제 가지고 있는 `id`를 사용해야 한다.)

> ```json
> {
>   "id": "11b3a825-30c5-551d-a713-dd748e7d554a"
> }
> ```

1. 블로그 게시물 페이지 템플릿에 페이지 쿼리를 추가한다.

   - `graphql` 태그를 반드시 import한다!
   - 쿼리 이름 `MyQuery` 을 제거한다. (MDX & 페이지 생성이 제대로 되도록 이름을 정의하지 않는다).

   ```jsx
   import * as React from "react";
   import {graphql} from "gatsby";
   import Layout from "../../components/layout";
   import Seo from "../../components/seo";

   const BlogPost = () => {
     return (
       <Layout pageTitle='Super Cool Blog Posts'>
         <p>My blog post contents will go here (eventually).</p>
       </Layout>
     );
   };

   export const query = graphql`
     query ($id: String) {
       mdx(id: {eq: $id}) {
         frontmatter {
           title
           date(formatString: "MMMM D, YYYY")
         }
       }
     }
   `;
   export const Head = () => <Seo title='Super Cool Blog Posts' />;

   export default BlogPost;
   ```

1. [Part 4](https://www.gatsbyjs.com/docs/tutorial/part-4/)에서 개츠비가 페이지 쿼리로부터 나온 결과를 `data` prop으로 페이지 컴포넌트에 전달한다는 사실을 배웠다. 개츠비는 또한 `data`를 개츠비 Head API로 전달한다. `BlogPost` 컴포넌트에 `data` prop을 추가하고 블로그 게시물 내용을 렌더링할 수 있다. 렌더링 준비가 된 실제 MDX 컨텐츠는 `children` prop으로 페이지 컴포넌트에 전달될 것이다.

   ```jsx
   import * as React from "react";
   import {graphql} from "gatsby";
   import Layout from "../../components/layout";
   import Seo from "../../components/seo";

   const BlogPost = ({data, children}) => {
     return (
       <Layout pageTitle={data.mdx.frontmatter.title}>
         <p>{data.mdx.frontmatter.date}</p>
         {children}
       </Layout>
     );
   };

   export const query = graphql`
     query ($id: String) {
       mdx(id: {eq: $id}) {
         frontmatter {
           title
           date(formatString: "MMMM D, YYYY")
         }
       }
     }
   `;

   export const Head = ({data}) => <Seo title={data.mdx.frontmatter.title} />;

   export default BlogPost;
   ```

1. `localhost:8000/blog/my-first-post/`와 같은 블로그의 게시물 페이지 한 곳에 가 보면, 게시물 컨텐츠가 개별 페이지에서 렌더링된 모습을 확인할 수 있다.
   - 모든 페이지가 게시물 컨텐츠를 제대로 렌더링했는지 확실히 하기 위해 다른 게시물에 대한 경로도 확인해 보자.

![https://www.gatsbyjs.com/static/423b31f3020b07bf46926ac91b1361a0/321ea/my-first-post.png](https://www.gatsbyjs.com/static/423b31f3020b07bf46926ac91b1361a0/321ea/my-first-post.png)

## 블로그 페이지 업데이트: 게시물 링크 삽입

여기까지 File System Route API와 GraphQL 쿼리 변수를 사용해서 블로그 게시물마다 개별 페이지를 생성했다.

이번 장에서는 마지막으로 Blog 페이지를 clean up한다. Blog 페이지에 게시물의 발췌(프리뷰)만 렌더링하는 대신, 생성해 놓은 새 게시물 링크를 넣을 수 있다.

1. `slug` 필드를 페이지 쿼리에 추가하고, 이를 사용해서 게시물 제목을 게시물이 나오는 페이지 링크로 전환한다.
   - 이들 링크는 사이트 내 페이지 사이에 있기 때문에([Since these links are between pages on your own site](https://www.gatsbyjs.com/docs/tutorial/part-6/#:~:text=Since%20these%20links%20are%20between%20pages%20on%20your%20own%20site)),개츠비의 `Link` 컴포넌트를 사용해서 성능 면에서 추가 이점을 얻을 수 있다.
   - absolute URLs(절대경로)를 사용한다면 path parameter `/blog/` 를 별도로 추가해야 한다. `slug` 필드는 `my-first-post`와 같은 path의 마지막 부분만을 포함하기 때문이다.

```jsx
import * as React from "react";
import {Link, graphql} from "gatsby";
import Layout from "../../components/layout";
import Seo from "../../components/seo";

const BlogPage = ({data}) => {
  return (
    <Layout pageTitle='My Blog Posts'>
      {data.allMdx.nodes.map((node) => (
        <article key={node.id}>
          <h2>
            <Link to={`/blog/${node.frontmatter.slug}`}>
              {node.frontmatter.title}
            </Link>
          </h2>
          <p>Posted: {node.frontmatter.date}</p>
        </article>
      ))}
    </Layout>
  );
};

export const query = graphql`
  query {
    allMdx(sort: {frontmatter: {date: DESC}}) {
      nodes {
        frontmatter {
          date(formatString: "MMMM D, YYYY")
          title
          slug
        }
        id
      }
    }
  }
`;

export const Head = () => <Seo title='My Blog Posts' />;

export default BlogPage;
```

1. 웹 브라우저에서 `[localhost:8000/blog/`을](http://localhost:8000/blog/을) 방문하면 Blog 페이지에 각 게시물 페이지로 이동하는 링크가 있음을 볼 수 있다.

   ![https://www.gatsbyjs.com/static/6526b4f1a8b71e2fcd5fece12be82b30/321ea/blog-page-with-links.png](https://www.gatsbyjs.com/static/6526b4f1a8b71e2fcd5fece12be82b30/321ea/blog-page-with-links.png)

이제 여러 페이지를 포함하는 블로그 사이트가 완성됐다! 새로운 `.mdx` 파일을 `blog` 디렉토리의 최상위에 추가해 보자. 사이트가 다시 빌드될 때 해당 파일이 Blog 페이지에 자동으로 더해질 것이다.

> 참고로, [GitHub repo for the example site](https://github.com/gatsbyjs/tutorial-example)에서 조합이 완성된 상태를 확인할 수 있다.

## 요약

지금까지 배웠던 내용을 잠시만 돌아보며 다음 질문에 답을 해 보자.

- File System Route API가 어디에 쓰이는지?
- 새로운 collection route를 생성하는 구문은?
- 쿼리 변수란?
- 쿼리 변수를 언제 사용할 수 있는지?

> **Ship It!** 🚀

사이트 변경 사항을 개츠비 클라우드에 배포해서 진행 경과를 공유할 수 있다.

먼저 터미널에서 다음의 명령을 실행해서 GitHub 레포지터리에 변경 사항을 push한다. (개츠비 사이트의 최상위 디렉토리에 있는지 확인이 필요하다.)

> ```bash
> git add .
> git commit -m "Finished Gatsby Tutorial Part 6"
> git push
> ```
>
> 변경 사항이 GitHub에 push되고 나면, Gatsby Cloud가 업데이트를 감지한 후 사이트의 최신 버전을 다시 빌드해서 배포한다. (변경한 부분이 사이트에 반영되기까지 몇 분 정도 시간이 걸린다. 빌드 진행 과정은 [Gatsby Cloud dashboard](https://www.gatsbyjs.com/dashboard/)에서 확인할 수 있다.)

### 핵심 내용

- 개츠비의 File System Route API는 특수 구문으로 파일 이름을 작성함으로써 데이터 레이어의 노드로부터 새 페이지를 동적으로 생성할 수 있도록 한다.
  - File System Routes는 the `src/pages` 디렉토리 (또는 서브 디렉토리)의 파일에 대해서만 작동한다.
  - 새로운 collection route를 생성하기 위해, 파일 이름을 `{nodeType.field}.js`으로 지정한다. 이 때 `nodeType`은 페이지를 생성하려는 노드의 타입이고 `filed`는 해당 페이지의 URL로 사용하고자 하는 노드 타입의 데이터 필드이다.
- Query variables let you pass in different data values to the same GraphQL query. They can be combined with field arguments to get back data only about a specific node. 쿼리 변수는 같은 GrpahQL 쿼리에 때마다 다른 데이터 값을 전달하도록 한다. 쿼리 변수는 특정 노드에 대한 데이터만 반환하도록 필드 인자와 결합될 수 있다.
- 쿼리 변수는 페이지 쿼리에서만 사용 가능하다.

### 다음에 배울 내용

파트 7에서는 `gatsby-plugin-image`를 다시 사용한다.([Part 3](https://www.gatsbyjs.com/docs/tutorial/part-3/)에 나왔던)

- `GatsbyImage` 컴포넌트를 사용해서 동적 이미지를 생성
- 블로그 게시물에 이미지를 추가함으로써 블로그 사이트 제작 마무리
