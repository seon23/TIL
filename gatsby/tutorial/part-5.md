# Part 5: MDX 사용 위해 데이터 변환

> 이 글은 **Gatsbyjs**의 "[Part 5: Transform Data to Use MDX](https://www.gatsbyjs.com/docs/tutorial/part-5/)"을 재가공한 것입니다. 원본 문서는 [MIT 라이선스](https://opensource.org/licenses/MIT)에 따라 사용할 수 있습니다.

<br>

<details><summary>목차</summary>
<p>

[소개](#소개)

[Gatsby의 GraphQL data layer 자세히 살펴보기](#gatsby의-graphql-data-layer-자세히-살펴보기)

[블로그 게시물에 MDX 컨텐츠 추가](#블로그-게시물에-mdx-컨텐츠-추가)

- [Frontmatter](#frontmatter)

[작성한 내용을 Blog 페이지에 렌더링 - `gatsby-plugin-mdx`](#작성한-내용을-blog-페이지에-렌더링---gatsby-plugin-mdx)

- [Task: MDX transformer plugin(및 dependency) 설치 후 설정](#task-mdx-transformer-plugin및-dependency-설치-후-설정)
- [Task: 블로그 페이지 쿼리 업데이트해서 allFile 대신 allMdx 필드 사용하기](#task-블로그-페이지-쿼리-업데이트해서-allfile-대신-allmdx-필드-사용하기)
- [Task: 게시물 발췌 부분을 블로그 페이지에 렌더링](#task-게시물-발췌-부분을-블로그-페이지에-렌더링)

[요약](#요약)

- [Key takeaways](#key-takeaways)
- [다음 장에서 배울 내용](#다음-장에서-배울-내용)

</p>
</details>

---

## 소개

`gatsby-source-filesystem`로 블로그 게시물 파일명 리스트가 있는 Blog 페이지를 빌드했었다. 그런데 이 소스 플러그인은 해당 게시물 파일의 컨텐츠를 render할 수 있는 필드를 제공하지 않는다. 이 기능을 위해선 **transofrmer plugin**이 필요하다.

웹사이트 빌드 시, source plugin에서 겟하는 데이터 포맷이 원하는 형태가 아닐 수도 있다. 예를 들면 `filesystem`으로는 파일에 _관한_ 쿼리 데이터를 요청할 수 있지만, 파일 _내부_ 데이터를 사용하게끔 하지는 않는다. 개츠비가 지원하는 `transformer` 플러그인으로는 이 작업이 가능하다. 이는 소스 플러그인으로부터 raw content를 take해서 더 사용에 적합한 형태로 변환한다.

<br>

튜토리얼 part 5에서 배울 내용

- `gatsby-plugin-mdx`: [MDX](https://mdxjs.com/), 즉 마크다운과 JSX를 허용하는 파일 포맷을 사용할 수 있다. (개츠비가 제공하는 튜토리얼 문서도 MDX로 작성되었다!)
- MDX로 블로그 게시물 파일에 일부 컨텐츠를 추가, `gatsby-plugin-mdx`로 게시물의 컨텐츠를 Blog 페이지에 render할 것이다.

> :memo: **Note:** transformer 플러그인명은 보통 `gatsby-transformer-`로 시작한다. 참고로 `gatsby-plugin-mdx`는 예외이다.<br> > [Gatsby Plugin Library](https://www.gatsbyjs.com/plugins)에서 `gatsby-transformer-`을 검색하면 다른 transformer 플러그인 리스트를 확인할 수 있다.

<br>

이 장을 끝내고 나면 아래 작업들이 가능해진다.

- 마크다운 formatting과 frontmatter로 MDX 파일 작성하기
- `gatsby-plugin-mdx` 사용하여, 작성한 MDX 파일의 컨텐츠를 Blog 페이지에 렌더링하기
- `sort`필드 사용하여 GrpahQL 쿼리 결과의 순서를 제어하기

<br>

## Gatsby의 GraphQL data layer 자세히 살펴보기

`gatsby-plugin-mdx`와 여타 transformer 플러그인이 어떻게 작동하는지를 이해하려면, Gatsby의 GrpahQL data layer의 작동 방식을 조금 더 알아볼 필요가 있다.

data layer 내부에서는 정보가 **nodes**라 불리는 객체에 저장되어 있다. node는 데이터 레이어 내 가장 작은 단위의 데이터이다. 소스 플러그인 각각은 서로 다른 타입의 node를 생성하고, 각 node는 고유한 프로퍼티를 갖는다. 예를 들어, `gatsby-source-filesystem`은 File 노드를 생성한다.

**transformer plugin**은 노드를 특정 타입에서 다른 타입으로 변환한다. 예를 들어, `gatsby-plugin-mdx`는 확장자가 `.mdx`인 File 노드를 MDX 노드로 변환하는데, 두 타입은 GraphQL를 사용해서 query할 수 있는 필드 집합이 다르다. transformer 플러그인으로, source 플러그인이 생성한 nodes 내 raw data를 처리해서 필요한 구조나 포맷으로 받을 수 있다.

![The gatsby-source-filesystem plugin pulls data from your local filesystem into Gatsby's GraphQL data layer as File nodes. Then the gatsby-plugin-mdx transformer plugin uses those File nodes to create new MDX nodes. Finally, you can use GraphQL queries to pull data from the MDX nodes into your components.](https://www.gatsbyjs.com/static/04384c192391ebeac3b63ea42872f3f2/7970d/data-layer-with-nodes.png)  
[_그림 출처_](https://www.gatsbyjs.com/docs/tutorial/part-5/)

<br>

> :memo: **Note:** transformer 플러그인이라 불린다고 해서 source 플러그인이 생성한 기존 노드를 실제로 _바꾸는_ 건 아니다. 각 transformer 플러그인은 sourced nodes의 data에 기반한 새로운 노드를 생성하지만, 실제로 source nodes 자체를 바꾸지는 않는다. 따라서 `gatsby-plugin-mdx`가 data layer 내에 새로운 MDX nodes를 생성할 지라도, `gatsby-source-filesystem`이 생성한 기존의 File nodes에 계속해서 접근할 수 있다.

이 장에서 transformer 플러그인을 사용하여 File nodes로부터 MDX nodes를 생성하는 법을 배울 것이다.

<br>

## 블로그 게시물에 MDX 컨텐츠 추가

Part 4에서 블로그 게시물을 염두에 둔 빈 파일을 생성했었다. 이제 내용물을 채워 넣을 때이다!

<br>

<details><summary><mark>MDX에서 마크다운 서식 사용하기</mark></summary>
<p>

MDX 파일을 사용하면 Markdown으로 텍스트 형식을 지정할 수 있다.

### Frontmatter

- `gatsby-plugin-mdx`로, **frontmatter**를 MDX 파일에 추가할 수도 있다. Frontmatter는 내 파일에 관한 추가 메타데이터이다. 페이지에 렌더링되지는 않지만페이지 내용 관련 세부사항을 추가할 좋은 수단이다. 예컨대, 게시물의 제목이나 발행 날짜를 저장할 수 있다.

<br>

frontmatter를 게시물에 추가하려면, 이를 (`---`) 사이에 넣어 MDX 파일 상단에 작성하면 된다. 파일 관련 저장하고 싶은 데이터면 무엇이든지 이에 대한 key-value 쌍을 (`---`) 안에 생성할 수 있다.

```MDX
---
name: "Fun Facts about Red Pandas"
datePublished: "2021-07-12"
author: "#1 Red Panda Fan"
---
```

게시물의 frontmatter에 접근하는 방법은 다음에 배울 것.

</p>
</details>

<br>

Part 4에서 만들었던 `/blog` 폴더에 있던`.mdx`파일에 Markdown 컨텐츠를 추가한다.

frontmatter에는 게시물 제목, 발행일, [slug](https://developer.mozilla.org/en-US/docs/Glossary/Slug) 필드가 들어간다. slug는 각 게시물 URL의 마지막 부분을 구성하는 데에 쓰인다. 후에 정렬하기 쉽도록 게시물마다 다른 날짜를 지정한다. frontmatter 다음에는 마크다운 문법으로 게시물 내용을 작성한다.

<details><summary>
참고할 만한 예시</summary>
<p>

```

---
title: "My First Post"
date: "2021-07-23"
slug: "my-first-post"
---

This is my first blog post! Isn't it *great*?

Some of my **favorite** things are:

* Petting dogs
* Singing
* Eating potato-based foods
```

```
---
title: "Another Post"
date: "2021-07-24"
slug: "another-post"
---

Here's another post! It's even better than the first one!
```

```
---
title: "Yet Another Post"
date: "2021-07-25"
slug: "yet-another-post"
---

Wow look at all this content. How do they do it?
```

</p>
</details>

<br>

## 작성한 내용을 Blog 페이지에 렌더링 - `gatsby-plugin-mdx`

`gatsby-plugin-mdx`는 GraphQL 쿼리에 대해 `allMdx`와 `mdx` 필드를 제공한다.

게시물을 Blog 페이지에 렌더링하려면 이전에 설치했던 플러그인의 경우와는 다른 단계를 거쳐야 한다.

1. `gatsby-plugin-mdx`과 이의 dependency를 설치하고 환경 설정을 변경한다.
2. Blog 페이지 쿼리에서, `allFile` 대신 `gatsby-plugin-mdx`가 제공하는 `allMdx` 필드를 사용한다.
3. 게시물의 발췌 부분을 Blog 페이지에 렌더링한다.

<br>

### Task: MDX transformer plugin(및 dependency) 설치 후 설정

`gatsby-plugin-mdx` 패키지는 dependency 하나를 추가로 실행할 것을 요구한다. 바로, MDX로 구현한 내용을 리액트 컴포넌트로 맵핑하는 `@mdx-js/react`이다.

1. 아래 명령어로 `gatsby-plugin-mdx`와 dependency를 설치한다. (그러면 `package.json`의 `dependencies` 객체와 `node_modules`폴더에 모두 추가된다.)

   ```sh
   npm install gatsby-plugin-mdx @mdx-js/react
   ```

2. 사이트 빌드 시 플러그인 사용 여부를 개츠비가 알도록 플러그`gatsby-plugin-mdx`를 `gatsby-config.js` 파일 내 `plugins` 배열에 추가한다.
   ```js:title=gatsby-config.js
     module.exports = {
       // 기존 코드
       plugins: [
         // 기존 플러그인
         "gatsby-plugin-mdx",
       ],
     };
   ```

<br>

> :bulb: **Tip:** 마크다운에 부가 기능을 추가할 수 있는 다양한 [remark](https://remark.js.org/) 플러그인이 있다. `gatsby-plugin-mdx`를 `gatsby-config.js` 파일에다 설정할 때 [`gatsbyRemarkPlugins`](https://www.gatsbyjs.com/plugins/gatsby-plugin-mdx/#gatsby-remark--plugins) 옵션을 추가할 수 있다.

<br>

> 유명한 remark 플러그인 몇 가지를 소개한다.
>
> - [`gatsby-remark-images`](https://www.gatsbyjs.com/plugins/gatsby-remark-images/): 마크다운 이미지 첨부 문법(`![alt](image url)`)을 사용해서 반응형 이미지를 만들고 싶을 때 사용한다.
>   - 이를 사용하려면 `gatsby-plugin-sharp`가 필요하다. (Part 3에서 이미 설치)
> - [`gatsby-remark-prismjs`](https://www.gatsbyjs.com/plugins/gatsby-remark-prismjs/): 코드 블럭에 하이라이팅 기능을 추가한다.
> - [`gatsby-remark-autolink-headers`](https://www.gatsbyjs.com/plugins/gatsby-remark-autolink-headers/): 마크다운 컨텐츠의 모든 헤더에 자동으로 링크를 걸어준다.

<br>

### Task: Blog 페이지 쿼리를 업데이트해서 `allFile` 대신 `allMdx` 필드 사용하기

`gatsby-plugin-mdx` 플러그인을 통해 새로운 필드(`allMdx`와 `mdx`)를 GraphQL 쿼리에서 사용할 수 있다. 이번 장에서는 `allMdx`를 사용해서 Blog 페이지에 작성해 놓은 컨텐츠(.mdx)를 추가한다. (`mdx` 필드는 Part6에서 사용 예정)

`allMdx`를 사용해서 다수의 MDX 노드 데이터를 한 번에 요청할 수 있다. (`allFile`이 File 노드에 접근했던 방식과 유사하다). GraphiQL을 열고 어떤 필드가 MDX 노드에서 사용 가능한지를 탐색해 보자.

> **Quick Refresher:** [“Use GraphiQL to explore the data layer and write GraphQL queries”](https://www.gatsbyjs.com/docs/tutorial/part-4/#use-graphiql-to-explore-the-data-layer-and-write-graphql-queries)에서 GraphiQL 접근 방법 참고

<br>

GraphiQL에서 `allFile` 대신 `allMdx` 필드를 사용해서 블로그 게시물 데이터를 가져오는 새로운 쿼리를 생성한다.

1. `allMdx` &rarr; `nodes` &rarr; `frontmatter` 드롭다운 목록을 차례로 클릭한다. 그러면 MDX 파일 내 frontmatter에서 생성했던 모든 키에 대한 필드를 볼 수 있다. `title`과 `date` 필드를 선택한다. `date` 필드의 인자로 `formatString`를 사용해 날짜가 표현되는 방식을 변경한다.

```
query MyQuery {
  allMdx {
    nodes {
      frontmatter {
        date(formatString: "MMMM D, YYYY")
        title
      }
    }
  }
}
```

<br>

> **Syntax Hink**: `formatString` 인자는 날짜가 보이는 방식을 변경할 때 유용한 도구이다.<br><br>
> frontmatter에서, `"YYYY-MM-DD"` 같은 날짜 포맷을 사용하는 key와 value를 가진다고 상상해 보자. (value만 필수 형식을 지키면 key에는 어떤 이름이 오든 상관 없다.) GraphiQL은 value가 날짜인지를 자동으로 감지하며, 상응하는 frontmatter field를 Explorer 창에서 선택할 때 GrahiQL은 해당 필드로 전달할 수 있는 인자 몇 개를 자동으로 보여준다. 그 인자 중 하나가 `formatString`인데, 이를 통해 [`Moment.js formatting token`](https://momentjs.com/docs/#/displaying/format/)을 전달해서 날짜 표시 방식을 변경할 수 있다.<br><br>
> 예시: MDX frontmatter가 다음과 같다면,

```MDX
date: "2023-02-20"
```

> 그리고 GraphQL 쿼리는 다음과 같다면,

```
query MyQuery {
  allMdx {
    nodes {
      frontmatter {
        date(formatString: "MMMM D, YYYY")
      }
    }
  }
}
```

> 응답 데이터는 `"February 20, 2023"`일 것이다.

2. 하는 김에, `id` 필드를 추가한다. 이건 개츠비가 데이터 레이어에 있는 모든 노드에 자동으로 추가하는 유니크한 문자열이다. (게시물 리스트를 iterate할 때, 이걸 React `key`로 사용할 수 있다.)

```
query MyQuery {
  allMdx {
    nodes {
      frontmatter {
        date(formatString: "MMMM D, YYYY")
        title
      }
      id
    }
  }
}
```

3. query를 실행한다. 응답 객체는 다음과 같이 보일 것이다.

```JSON
{
  "data": {
    "allMdx": {
      "nodes": [
        {
          "frontmatter": {
            "date": "July 25, 2021",
            "title": "Yet Another Post"
          },
          "id": "c4b5ae6d-f3ad-5ea4-ab54-b08a72badea1"
        },
        {
          "frontmatter": {
            "date": "July 23, 2021",
            "title": "My First Post"
          },
          "id": "11b3a825-30c5-551d-a713-dd748e7d554a"
        },
        {
          "frontmatter": {
            "date": "July 24, 2021",
            "title": "Another Post"
          },
          "id": "560896e4-0148-59b8-9a2b-bf79bee68fba"
        }
      ]
    }
  },
  "extensions": {}
}
```

4. 게시물이 순서대로 나열되지 않았다. 대부분의 블로그 사이트는 게시물을 역순으로 나열해서 최신 게시물이 처음으로 배치된다. `allMdx` 필드에서 `sort` 인자를 사용함으로써 해당 데이터 노드를 정렬할 수 있다.
   - Explorer 창에서, `allMdx` 필드 아래에 있는 `sort`를 클릭한다.
   - `sort` 아래에 `frontmatter` 인자를 체크, date 드롭다운 목록에서 `DESC`를 선택한다. 그러면 노드가 내림차순(descending order)으로 정렬되어 마지막에 올린 게시물이 처음에 배치된다.

<br>

```
query MyQuery {
  allMdx(sort: { frontmatter: { date: DESC } }) {
    nodes {
      frontmatter {
        date(formatString: "MMMM D, YYYY")
        title
      }
      id
    }
  }
}
```

5. 예상 쿼리 실행 결과는 다음과 같다.

```JSON
{
  "data": {
    "allMdx": {
      "nodes": [
        {
          "frontmatter": {
            "date": "July 25, 2021",
            "title": "Yet Another Post"
          },
          "id": "c4b5ae6d-f3ad-5ea4-ab54-b08a72badea1"
        },
        {
          "frontmatter": {
            "date": "July 24, 2021",
            "title": "Another Post"
          },
          "id": "560896e4-0148-59b8-9a2b-bf79bee68fba"
        },
        {
          "frontmatter": {
            "date": "July 23, 2021",
            "title": "My First Post"
          },
          "id": "11b3a825-30c5-551d-a713-dd748e7d554a"
        }
      ]
    }
  },
  "extensions": {}
}
```

6. 게시물 프리뷰도 쿼리에 추가하자. (`excerpt` 필드)

```
query MyQuery {
  allMdx(sort: { frontmatter: { date: DESC } }) {
    nodes {
      frontmatter {
        date(formatString: "MMMM D, YYYY")
        title
      }
      id
      excerpt
    }
  }
}
```

7. 쿼리를 실행하면 `excerpt` 필드 데이터는 다음과 같을 것이다

```JSON
"excerpt": "Wow look at all this content. How do they do it?"
```

> :bulb: **Pro Tip:** transformer 플러그인은 새로운 노드를 생성할 때 이 노드의 오리지널 소스 노드를 참조하는 `parent` 필드를 추가한다. 예를 들어, `gatsby-plugin-mdx`는 새 MDX 노드를 생성할 때, 오리지널 File 노드의 데이터에 접근하는 데에 사용할 `parent`필드를 추가한다.<br>
> transformed nodes의 데이터와 original source nodes의 데이터를 함께 사용하고 싶을 때 `parent` 노드는 유용하다. 예를 들어, 아래 쿼리는 파일이 변경된 시간을 알려주어서, 게시물이 마지막으로 업데이트된 때를 표기할 때 사용할 수 있다.

<br>

```
query MyQuery {
  allMdx {
    nodes {
      parent {
        ... on File {
          modifiedTime(formatString: "MMMM D, YYYY")
        }
      }
    }
  }
}
```

<br>

### Task: 게시물 발췌 부분을 블로그 페이지에 렌더링

Blog 페이지 컴포넌트 내 기존 페이지 쿼리를 세팅한 GraphQL 쿼리로 변경한다.

1. `allFile`로 만든 페이지 쿼리를 `allMdx`로 생성한 것으로 변경한다. (쿼리 이름을 반드시 지운다!)

2. 받은 데이터 필드를 사용하기 위해 JSX 부분을 변경한다.

   - filename 이외에도 다른 걸 렌더링할 것이므로, `<ul>`과 `<li>` 엘리먼트 대신 HTML의 시맨틱 엘리먼트 `<article>`을 사용한다.
   - `id` 필드를 각 게시물의 유니크한 `key` prop으로 사용할 수 있다. (리액트는 `key` prop을 사용해서 재렌더링이 필요한 엘리먼트를 추적한다. 이를 포함하지 않을 경우 브라우저 콘솔에서 경고 메시지가 뜬다. `key` prop에 대한 자세한 설명은 [React Docs: List and Keys.](https://reactjs.org/docs/lists-and-keys.html#keys)을 참고)

3. In the JSX for your Blog page render the value of the excerpt field. (JSX에 `excerpt` 필드 값 추가)

<br>

```js
// imports

const BlogPage = ({data}) => {
  return (
    <Layout pageTitle='My Blog Posts'>
      {data.allMdx.nodes.map((node) => (
        <article key={node.id}>
          <h2>{node.frontmatter.title}</h2>
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
          title
          date(formatString: "MMMM DD, YYYY")
        }
        id
        excerpt
      }
    }
  }
`;

export const Head = () => <Seo title='My Blog Posts' />;

export default BlogPage;
```

4. `localhost:8000/blog` 화면
   ![각 게시물의 제목, 날짜, 프리뷰를 보여주는 Blog 페이지](../../../images/blog-page-with-posts-date-exceprt.JPG)

> :memo: **Note:** 쿼리가 반환한 excerpt는 HTML 태그를 포함하지 않아서, "My First Post"에 작성했던 순서없는 리스트가 제대로 렌더링되지 않는다. 이 부분은 후에 교정할 것이다.

<br>

## 요약

복습

- transformer plgin이란? transformer 플러그인이 데이터 레이어 내 데이터에 미치는 영향은?
- MDX란? MDX가 유용한 이유는?

> **참고:** 변경한 내용을 GitHub에 푸시하면, Gatsby Cloud는 업데이트 내용을 인식하고 사이트를 최신 버전으로 다시 빌드해서 배포한다. (빌드 진행 과정은 [Gatsby Cloud dashboard](https://www.gatsbyjs.com/dashboard/)에서 확인)

<br>

### Key takeaways

- Gatsby의 GrpahQL 데이터 레이어에 있는 데이터는 **nodes**에 저장된다.
- 각 소스 플러그인은 다른 필드를 다른 타입의 노드를 생성한다.
- [transformer 플러그인은 기존 소스 노드의 데이터를 시작점으로 사용해서 새로운 노드를 생성한다. transformer 플러그인은 기존 소스 노드를 변경하지는 않는다.](https://www.gatsbyjs.com/docs/tutorial/part-5/#:~:text=Transformer%20plugins%20create%20new%20types%20of%20nodes%2C%20using%20data%20from%20existing%20source%20nodes%20as%20a%20starting%20point.%20Transformer%20plugins%20don%E2%80%99t%20actually%20change%20the%20original%20source%20nodes.)
- `gatsby-plugin-mdx`는 MDX를 사용할 수 있게 하는 transformer 플러그인이다. MDX를 통해, 마크다운 서식과 내장 리액트 컴포넌트로 작성한 텍스트 컨텐츠를 생성할 수 있다.

<br>

### 다음 장에서 배울 내용

개츠비가 제공하는 filesystem route API를 사용해서, 각 게시물에 대한 새로운 페이지를 동적으로 생성하는 방법
