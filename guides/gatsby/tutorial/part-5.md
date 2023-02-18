# Part 5: MDX 사용 위해 데이터 변환
<details><summary>목차</summary>
<p>

[소개](#소개)

[Gatsby의 GraphQL data layer 자세히 살펴보기](#gatsby의-graphql-data-layer-자세히-살펴보기)

[블로그 게시물에 MDX 컨텐츠 추가](#블로그-게시물에-mdx-컨텐츠-추가)
- [Frontmatter](#frontmatter)
[각 게시물의 컨텐츠를 블로그 페이지에 렌더링](#각-게시물의-컨텐츠를-블로그-페이지에-렌더링)
- [Task: MDX transformer plugin(및 dependency) 설치 후 configure](#task-mdx-transformer-plugin및-dependency-설치-후-configure)
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

웹사이트 빌드 시, source plugin에서 겟하는 데이터 포맷이 원하는 형태가 아닐 수도 있다. 예를 들면 `filesystem`으로는 파일에 *관한* 쿼리 데이터를 요청할 수 있지만, 파일 *내부* 데이터를 사용하게끔 하지는 않는다. 개츠비가 지원하는 `transformer` 플러그인으로는 이 작업이 가능하다. 이는 소스 플러그인으로부터 raw content를 take해서 더 사용에 적합한 형태로 변환한다.

튜토리얼 part 5에서 배울 내용
- `gatsby-plugin-mdx`: [MDX](https://mdxjs.com/), 즉 마크다운과 JSX를 허용하는 파일 포맷을 사용할 수 있다. (개츠비가 제공하는 튜토리얼 문서도 MDX로 작성되었다!)  
- MDX로 블로그 게시물 파일에 일부 컨텐츠를 추가, `gatsby-plugin-mdx`로 게시물의 컨텐츠를 Blog 페이지에 render할 것이다.

> :memo: **Note:** transformer 플러그인명은 보통 `gatsby-transformer-`로 시작한다. 참고로 `gatsby-plugin-mdx`는 예외이다.
<br>
[Gatsby Plugin Library](https://www.gatsbyjs.com/plugins/)에서 `gatsby-transformer-`을 검색하면 다른 transformer 플러그인 리스트를 확인할 수 있다.

이 장을 끝내고 나면 아래 작업들이 가능해진다.
- 마크다운 formatting과 frontmatter로 MDX 파일 작성하기
- `gatsby-plugin-mdx` 사용하여, 작성한 MDX 파일의 컨텐츠를 Blog 페이지에 렌더링하기
- `sort`필드 사용하여 GrpahQL 쿼리 결과의 순서를 제어하기


## Gatsby의 GraphQL data layer 자세히 살펴보기
`gatsby-plugin-mdx`와 여타 transformer 플러그인이 어떻게 작동하는지를 이해하려면, Gatsby의 GrpahQL data layer의 작동 방식을 조금 더 알아볼 필요가 있다.

data layer 내부에서는 정보가 **nodes**라 불리는 객체에 저장되어 있다. node는 데이터 레이어 내 가장 작은 단위의 데이터이다. 소스 플러그인 각각은 서로 다른 타입의 node를 생성하고, 각 node는 고유한 프로퍼티를 갖는다. 예를 들어, `gatsby-source-filesystem`은 File 노드를 생성한다.

**transformer plugin**은 노드를 특정 타입에서 다른 타입으로 변환한다. 예를 들어, `gatsby-plugin-mdx`는 확장자가 `.mdx`인 File 노드를 MDX 노드로 변환하는데, 두 타입은 GraphQL를 사용해서 query할 수 있는 필드 집합이 다르다. transformer 플러그인으로, source 플러그인이 생성한 nodes 내 raw data를 처리해서 필요한 구조나 포맷으로 받을 수 있다.

![The gatsby-source-filesystem plugin pulls data from your local filesystem into Gatsby's GraphQL data layer as File nodes. Then the gatsby-plugin-mdx transformer plugin uses those File nodes to create new MDX nodes. Finally, you can use GraphQL queries to pull data from the MDX nodes into your components.](https://www.gatsbyjs.com/static/04384c192391ebeac3b63ea42872f3f2/7970d/data-layer-with-nodes.png)  
[*그림 출처*](https://www.gatsbyjs.com/docs/tutorial/part-5/)

> :memo: **Note:** transformer 플러그인이라 불린다고 해서 source 플러그인이 생성한 기존 노드를 실제로 *바꾸는* 건 아니다. 각 transformer 플러그인은 sourced nodes의 data에 기반한 새로운 노드를 생성하지만, 실제로 source nodes 자체를 바꾸지는 않는다. 따라서 `gatsby-plugin-mdx`가 data layer 내에 새로운 MDX nodes를 생성할 지라도, `gatsby-source-filesystem`이 생성한 기존의 File nodes에 계속해서 접근할 수 있다.

이 장에서 transformer 플러그인을 사용하여 File nodes로부터 MDX nodes를 생성하는 법을 배울 것이다.

## 블로그 게시물에 MDX 컨텐츠 추가
Part 4에서 블로그 게시물을 염두에 둔 빈 파일을 생성했었다. 이제 내용물을 채워 넣을 때이다!

<details><summary><mark>MDX에서 마크다운 서식 사용하기</mark></summary>
<p>

MDX 파일을 사용하면 Markdown으로 텍스트 형식을 지정할 수 있다. 

### Frontmatter
- `gatsby-plugin-ndx`로, **frontmatter**를 MDX 파일에 추가할 수도 있다. Frontmatter는 내 파일에 관한 추가 메타데이터이다. 페이지에 렌더링되지는 않지만페이지 내용 관련 세부사항을 추가할 좋은 수단이다. 예컨대, 게시물의 제목이나 발행 날짜를 저장할 수 있다.

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

Part 4에서 만들었던 `/blog` 폴더에 있던`.mdx`파일에 Markdown 컨텐츠를 추가한다.


frontmatter에는 게시물 제목, 발행일, [slug](https://developer.mozilla.org/en-US/docs/Glossary/Slug) 필드가 들어간다. slug는 각 게시물 URL의 마지막 부분을 구성하는 데에 쓰인다. 후에 정렬하기 쉽도록 게시물마다 다른 날짜를 지정한다. frontmatter 다음에는 마크다운 문법으로 게시물 내용을 작성한다.

<details><summary>
아래는 참고할 만한 예시이다.</summary>
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

## 작성한 내용을 Blog 페이지에 렌더링 - `gatsby-plugin-mdx`


`gatsby-plugin-mdx`는 GraphQL 쿼리에 대해 `allMdx`와 `mdx` 필드를 제공한다.

게시물을 Blog 페이지에 렌더링하려면 이전에 설치했던 플러그인의 경우와는 다른 단계를 거쳐야 한다.
1. `gatsby-plugin-mdx`과 이의 dependency를 설치하고 환경 설정을 변경한다.
2. Blog 페이지 쿼리에서, `allFile` 대신 `gatsby-plugin-mdx`가 제공하는 `allMdx` 필드를 사용한다.
3. 게시물의 발췌 부분을 Blog 페이지에 렌더링한다.

### Task: MDX transformer plugin(및 dependency) 설치 후 설정
`gatsby-plugin-mdx` 패키지는 dependency 하나를 추가로 실행할 것을 요구한다. 바로, MDX로 구현한 내용을 리액트 컴포넌트로 맵핑하는 `@mdx-js/react`이다. 
1. 터미널 커맨드에서 `gatsby-plugin-mdx`와 dependency를 설치한다. (이는 `package.json` 파일 내 `dependencies` 객체와 `node_modules`폴더에 패키지 두 개를 추가한다.)  
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
> :bulb: **Tip:** 마크다운에 부가 기능을 추가할 수 있는 다양한 [remark](https://remark.js.org/) 플러그인이 있다. `gatsby-plugin-mdx`를 `gatsby-config.js` 파일에다 설정할 때 [`gatsbyRemarkPlugins`](https://www.gatsbyjs.com/plugins/gatsby-plugin-mdx/#gatsby-remark--plugins) 옵션을 추가할 수 있다.
<br><br>
> 유명한 remark 플러그인 몇 가지를 소개한다.
>- [`gatsby-remark-images`](https://www.gatsbyjs.com/plugins/gatsby-remark-images/): 마크다운 이미지 첨부 문법(`![alt](image url)`)을 사용해서 반응형 이미지를 만들고 싶을 때 사용한다.
>   - 이를 사용하려면 `gatsby-plugin-sharp`가 필요하다. (Part 3에서 이미 설치)
>- [`gatsby-remark-prismjs`](https://www.gatsbyjs.com/plugins/gatsby-remark-prismjs/): 코드 블럭에 하이라이팅 기능을 추가한다.
>- [`gatsby-remark-autolink-headers`](https://www.gatsbyjs.com/plugins/gatsby-remark-autolink-headers/): 마크다운 컨텐츠의 모든 헤더에 자동으로 링크를 걸어준다.  

### Task: 블로그 페이지 쿼리 업데이트해서 allFile 대신 allMdx 필드 사용하기

### Task: 게시물 발췌 부분을 블로그 페이지에 렌더링
## 요약
### Key takeaways
### 다음 장에서 배울 내용