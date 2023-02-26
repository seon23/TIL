# Part 7: 데이터에서부터 동적 이미지 추가

> 이 글은 **Gatsbyjs**의 "[Part 7: Add Dynamic Images from Data](https://www.gatsbyjs.com/docs/tutorial/part-7/)"을 재가공한 것입니다. 원본 문서는 [MIT 라이선스](https://opensource.org/licenses/MIT)에 따라 사용할 수 있습니다.

<details><summary>목차</summary>
<p>

[소개](#소개)

[`GatsbyImage`와 `StaticImage`의 차이점](#gatsbyimage와-staticimage의-차이점)

[블로그 게시물 frontmatter에 히어로 이미지(Hero Image) 추가](#블로그-게시물-frontmatter에-히어로-이미지-추가)

[`gatsby-transformer-sharp` 설치 및 구성(configuration)](#gatsby-transformer-sharp-설치-및-구성configuration)

[블로그 게시물 페이지 템플릿에 히어로 이미지 렌더링](#블로그-게시물-페이지-템플릿에-히어로-이미지-렌더링)

- [작업: GraphiQL 사용하여 쿼리 빌드](#작업-graphiql-사용하여-쿼리-빌드)
- [작업: `GatsbyImage` 컴포넌트 사용하여 히어로 이미지 추가](#작업-gatsbyimage-컴포넌트-사용하여-히어로-이미지-추가)
- [작업: 히어로 이미지 밑에 크레딧 추가](#작업-히어로-이미지-밑에-크레딧-추가)

[요약](#요약)

- [핵심 내용](#핵심-내용)
- [개츠비 튜토리얼 완료](#개츠비-튜토리얼-완료)

</p>
</details>

## 소개

[Part 3](/docs/tutorial/part-3/)에서 `gatsby-plugin-image`를 사용하여 Home 페이지에 정적 이미지를 추가했었다. 개츠비의 데이터 레이어로 작업을 조금 해 보았으니, 이제 `gatsby-plugin-image`얘기를 다시 할 차례이다. 여기에서는 사이트에 동적 이미지를 추가하는 방법을 배울 것이다.

이번 장에서는 동적 `GatsbyImage` 컴포넌트를 사용하여 블로그 게시물 각각에 히어로 이미지(hero image)를 추가해 본다.

이 장을 마치고 나면 아래의 작업을 할 수 있다.

- `GatsbyImage` 컴포넌트를 사용하여 데이터로부터 동적 이미지 생성

<br>

## `GatsbyImage`와 `StaticImage`의 차이점

[Part 3](/docs/tutorial/part-3/)에서는 `gatsby-plugin-image`으로부터 `StaticImage` 컴포넌트를 사용하는 방법을 설명했었다.

`StaticImage` 컴포넌트를 사용해야 하는지, `GatsbyImage` 컴포넌트를 사용해야 하는지, 어떻게 알 수 있을까? 이는 컴포넌트를 렌더링하는 모든 시기마다 동일한 이미지 소스를 사용할 건지에 대한 문제이다.

- `StaticImage`는 하드 코딩으로 작성된 파일 경로나 원격 URL과 같은 '정적' 이미지 소스를 위한 컴포넌트이다. 즉, 이 컴포넌트를 렌더링할 때마다 이미지 소스는 항상 동일하다.
- `GatsbyImage`는 prop으로 전달되는 이미지 소스같은 '동적' 이미지 소스를 위한 컴포넌트이다.
  <br>

간단한 비유로 차이점을 설명하자면 다음과 같다.

- `StaticImage` 컴포넌트 사용은, "400 메인 스트리트"와 같은 물리 주소(physical address)로 방향을 묻는 것과 같다. 얼마나 많은 사람에게 물었는지와 상관없이 결국엔 같은 장소에 도착하는 경우이다.
- `GatsbyImage` 컴포넌트 사용은, 보다 포괄적으로 방향을 묻는 것과 같다. 누군가에게 마을 내 최고의 커피숍을 묻는다면 누구에게 물었는지와 그 사람의 개인적인 선호가 무엇인지에 따라 결과가 달라진다.

![StaticImage와 GatsbyImage 컴포넌트의 차이](https://www.gatsbyjs.com/static/c6fccf3d19b405ef92dd65472a6fb8f4/a5262/staticimage-vs-gatsbyimage-towns.png)
_Image by Gatsbyjs, from https://www.gatsbyjs.com/docs/tutorial/part-7/, licensed under [MIT](https://opensource.org/licenses/MIT)._

<br>

이번 장에서는 블로그 게시물 페이지 템플릿에 히어로 이미지를 추가할 것이다. 또한 각 게시물마다 사용할 이미지를 명시하기 위해 블로그 게시물 `.mdx` 파일에 frontmatter 데이터를 추가할 것이다. 페이지 템플릿에 로드하는 이미지 소스가 게시물마다 다르기 때문에 `GatsbyImage` 컴포넌트를 사용한다.

<br>

## 블로그 게시물 frontmatter에 히어로 이미지(Hero Image) 추가

많은 블로그 사이트에서 게시물 상단에 히어로 이미지를 사용하고 있다. 이런 이미지는 대개 크고 고품질인 사진으로, 독자의 시선을 사로잡아 해당 페이지에 더 오래 머물고 싶게끔 한다.

아래 단계를 통해 히어로 이미지로 적합한 사진을 찾아 다운 받을 수 있으며, 블로그 게시물의 frontmatter에 추가할 수 있다.

<br>

1. 모든 MDX 게시물이 포함된 `blog` 디렉토리를 구성하면서 시작한다. 먼저 `blog` 폴더 안에 각 게시물에 대한 하위 디렉토리를 생성한다. 그 다음 각 `.mdx` 파일의 이름을 `index.mdx`로 변경한다. (route가 `blog/my-post/my-post/`같은 중복 path parameter를 갖게 되는 상황을 막기 위함)
   - 예를 들면 `blog/my-first-post.mdx`의 게시물이 `blog/my-first-post/index.mdx`로 이동한다. 마찬가지로 `blog/another-post.mdx`의 게시물이 `blog/another-post/index.mdx`로 이동한다.

> **Note:** `.mdx`파일의 위치나 이름을 바꾼 후, 변경 사항이 적용되도록 로컬 개발 서버를 중단하고 다시 시작해야 한다.

![Diagram of moving each MDX file into its own subdirectory in the blog folder](https://www.gatsbyjs.com/static/f7150a9cc0e9f60b6d51c544ecc79117/1816f/reorganize-blog-directory.png)
_Image by Gatsbyjs, from https://www.gatsbyjs.com/docs/tutorial/part-7/, licensed under [MIT](https://opensource.org/licenses/MIT)._

2. [Unsplash](https://unsplash.com) 같은 웹사이트를 사용하여 자유롭게 사용할 수 있는 예쁜 이미지를 찾는다. 화면에 잘 맞도록 가로 방향 이미지를 선택한다.
   ![A screenshot of the Unsplash home page](https://www.gatsbyjs.com/static/c8cfa84caf6e939eb724737f03e6943f/60b3a/unsplash.png)
   _Image from [Gatsby site](https://www.gatsbyjs.com/docs/tutorial/part-7/)_

3. 마음에 드는 사진을 찾았으면 다운 받아 이를 특정 게시물에 대한 하위 디렉토리에 추가한다.
   ![Diagram of the "blog" folder structure, with the hero image for each post in the subdirectory for that post.](https://www.gatsbyjs.com/static/5cc60b117349ec93e6f1b59b34917a8e/60b3a/folder-structure-with-images.png)
   _Image by Gatsbyjs, from https://www.gatsbyjs.com/docs/tutorial/part-7/, licensed under [MIT](https://opensource.org/licenses/MIT)._

> **Pro Tip:** 간혹 인터넷에서 다운로드한 이미지 품질이 너무 높을 때가 있다. 사이트에서 너비가 1000픽셀인 이미지까지만 렌더링한다면, 너비가 5000픽셀인 소스 이미지를 가져봐야 소용이 없다. 픽셀 제한을 초과하면, 이미지를 처리하고 최적화하는 작업이 더 필요하기 때문에 빌드 시간이 늘어날 수 있다.
>
> 일반적인 지침으로는 이미지 파일을, 렌더링될 수 있는 최대 크기의 2배 이하로 조정함으로써 **미리 최적화**하는 것이 좋다. 예를 들어 레이아웃 너비가 600픽셀이라면, 가능한 이미지의 최고 해상도는 1200픽셀이다.
>
> [Preoptimizing Your Images](/docs/preoptimizing-images/)의 문서에서 더 자세한 정보를 확인할 수 있다..

<br>

3. 다음으로, 블로그의 각 페이지에 frontmatter를 추가로 넣는다.
   - `hero_image`: 게시물에 넣을 히어로 이미지 파일의 상대 경로
   - `hero_image_alt`: 이미지에 대한 짧은 설명. 스크린 리더가 인식하는 대체 텍스트로 사용, 또는 이미지가 제대로 로딩되지 않은 경우에 사용.
   - `hero_image_credit_text`: 히어로 이미지 크레딧 텍스트
   - `hero_image_credit_link`: 히어로 이미지를 다운로드한 페이지 링크

```mdx:title=blog/my-first-post/index.mdx
---
title: "My First Post"
date: "2021-07-23"
slug: "my-first-post"
hero_image: "./christopher-ayme-ocZ-_Y7-Ptg-unsplash.jpg"
hero_image_alt: "A gray pitbull relaxing on the sidewalk with its tongue hanging out"
hero_image_credit_text: "Christopher Ayme"
hero_image_credit_link: "https://unsplash.com/photos/ocZ-_Y7-Ptg"
---

...
```

```mdx:title=blog/another-post/index.mdx
---
title: "Another Post"
date: "2021-07-24"
slug: "another-post"
hero_image: "./anthony-duran-eLUBGqKGdE4-unsplash.jpg"
hero_image_alt: "A grey and white pitbull wading happily in a pool"
hero_image_credit_text: "Anthony Duran"
hero_image_credit_link: "https://unsplash.com/photos/eLUBGqKGdE4"
---

...
```

```mdx:title=blog/yet-another-post/index.mdx
---
title: "Yet Another Post"
date: "2021-07-25"
slug: "yet-another-post"
hero_image: "./jane-almon-7rriIaBH6JY-unsplash.jpg"
hero_image_alt: "A white pitbull wearing big googly-eye glasses"
hero_image_credit_text: "Jane Almon"
hero_image_credit_link: "https://unsplash.com/photos/7rriIaBH6JY"
---

...
```

이미지가 준비되었으니, 블로그 게시물 페이지 템플릿에 히어로 이미지를 pull할 수 있도록 이를 데이터 레이어에 연결한다.

<br>

## `gatsby-transformer-sharp` 설치 및 구성(configuration)

`GatsbyImage` 컴포넌트를 사용하기 위해, `gatsby-transformer-sharp` transformer plugin을 사이트에 추가한다.

Gatsby가 빌드 시간에 노드를 데이터 레이어에 추가할 때, `gatsby-transformer-sharp` plugin은 `.png`, `.jpg` 같은 이미지 확장자를 갖는 모든 `File` 노드를 찾는다. 그리고 이 파일에 대한 `ImageSharp` 노드를 생성한다.

![A diagram showing how ImageSharp nodes get created from File nodes in the data layer](https://www.gatsbyjs.com/static/d710adc16d08d60f8919c62468ec0e8f/a5262/data-layer-with-imagesharp-nodes.png)
_Image by Gatsbyjs, from https://www.gatsbyjs.com/docs/tutorial/part-7/, licensed under [MIT](https://opensource.org/licenses/MIT)._

<br>

1. In the terminal, run the following command to install `gatsby-transformer-sharp`를 설치하기 위해 터미널에서 다음의 명령을 실행한다.

   ```shell
   npm install gatsby-transformer-sharp
   ```

2. `gatsby-transformer-sharp`를 `gatsby-config.js` 파일의 `plugins` 배열에 추가한다.
   ```js:title=gatsby-config.js
   module.exports = {
     siteMetadata: {
       title: "My First Gatsby Site",
     },
     plugins: [
       // ...existing plugins
       "gatsby-transformer-sharp",
     ],
   }
   ```

`gatsby-transformer-sharp`을 사이트에 추가했기 때문에, GraphiQL에서 변경사항을 확인하기 위해 로컬 개발 서버를 중단한 후 다시 시작해야 한다. 다음 단계에서 GraphiQL을 더 자세히 살펴볼 것이다.

<br>

## 블로그 게시물 페이지 템플릿에 히어로 이미지 렌더링

필수 도구들이 준비되었으니 이제 히어로 이미지를 블로그 게시물 페이지 템플릿에 추가할 수 있다.

### 작업: GraphiQL 사용하여 쿼리 빌드

먼저, GraphiQL을 사용하여 히어로 이미지 관련 frontmatter 필드를 블로그 게시물 페이지 템플릿의 GraphQL 쿼리에 추가한다.

1. 웹 브라우저에서 `localhost:8000/___graphql`을 방문하여 GraphiQL을 연다. 현재 블로그 게시물 페이지의 쿼리를 GrpahiQL 쿼리 편집창에 복사하면서 시작한다. 한 번 실행해서 모든 작업이 제대로 이루어지는 지 확인한다.

> **Note:** Query Variables 창에서 특정 게시물에 대한 `id`를 가지고 객체를 설정해야할 것이다. 설정하는 법은 [Part 6 section on query variables](https://www.gatsbyjs.com/docs/tutorial/part-6/#render-post-contents-in-the-blog-post-page-template)을 참고하면 된다.

```graphql
query ($id: String) {
  mdx(id: {eq: $id}) {
    frontmatter {
      title
      date(formatString: "MMMM D, YYYY")
    }
  }
}
```

```json
{
  "data": {
    "mdx": {
      "frontmatter": {
        "title": "My First Post",
        "date": "July 23, 2021"
      }
    }
  },
  "extensions": {}
}
```

2. 탐색 창에서 `hero_image_alt`, `hero_image_credit_link`, `hero_image_credit_text` 필드를 선택한다. 쿼리를 실행하면 아래의 JSON 객체와 같은 응답을 받는다.

> **Note:** 위 필드는 `frontmatter:` 인자(옥색) 말고, 그 아래에 있는 `frontmatter` 필드(파란색) 목록에서 확인할 수 있다.

```graphql
query ($id: String) {
  mdx(id: {eq: $id}) {
    frontmatter {
      title
      date(formatString: "MMMM D, YYYY")
      hero_image_alt
      hero_image_credit_link
      hero_image_credit_text
    }
  }
}
```

```json
{
  "data": {
    "mdx": {
      "frontmatter": {
        "title": "My First Post",
        "date": "July 23, 2021",
        "hero_image_alt": "A gray pitbull relaxing on the sidewalk with its tongue hanging out",
        "hero_image_credit_link": "https://unsplash.com/photos/ocZ-_Y7-Ptg",
        "hero_image_credit_text": "Christopher Ayme"
      }
    }
  },
  "extensions": {}
}
```

3. `hero_image` 필드 내 `childImageSharp` 토글 버튼을 클릭, `gatsbyImageData` 필드를 선택한다. 이제 쿼리는 다음처럼 보여야 한다.

```graphql
query ($id: String) {
  mdx(id: {eq: $id}) {
    frontmatter {
      title
      date(formatString: "MMMM D, YYYY")
      hero_image_alt
      hero_image_credit_link
      hero_image_credit_text
      hero_image {
        childImageSharp {
          gatsbyImageData
        }
      }
    }
  }
}
```

> **Pro Tip:** GraphiQL은 frontmatter의 `hero_image` 필드에 다른 필드를 추가한다는 걸 어떻게 알까?
>
> Gatsby는 사이트를 빌드할 때 데이터 레이어의 다양한 타입의 데이터를 설명하는**schema**를 생성한다. Gatsby는 해당 스키마를 빌드하면서 각 필드의 데이터 타입을 추측하려 시도한다. 이러한 과정을 **schema inference**라고 한다.
>
> Gatsby는 MDX frontmatter의 `hero_image` 필드가 `File` 노드에 상응함을(match) 알린다. 그래서 그 노드에 대한 `File` 필드를 요청할(query) 수 있는 것이다. 마찬가지로, `gatsby-transformer-sharp`가 해당 파일 이미지임을 알려, 그 노드에 대한 `ImageSharp` 필드를 요청할 수 있는 것이다.([정확한 원문](https://www.gatsbyjs.com/docs/tutorial/part-7/#:~:text=Gatsby%20can%20tell%20that%20the%20hero_image%20field%20from%20your%20MDX%20frontmatter%20matches%20a%20File%20node%2C%20so%20it%20lets%20you%20query%20the%20File%20fields%20for%20that%20node.%20Similarly%2C%20gatsby%2Dtransformer%2Dsharp%20can%20tell%20that%20the%20file%20is%20an%20image%2C%20so%20it%20also%20lets%20you%20query%20the%20ImageSharp%20fields%20for%20that%20node.))

<br>

4. 응답으로 무슨 데이터를 받을지 확인하기 위해 쿼리를 실행해 보자. 이전에 받아었던 응답과 대부분 유사하겠지만, 이번에는 `hero-image` 객체가 추가된다.

```json
{
  "data": {
    "mdx": {
      "frontmatter": {
        // ...
        "hero_image": {
          "childImageSharp": [
            {
              "gatsbyImageData": {
                "layout": "constrained",
                "backgroundColor": "#282828",
                "images": {
                  "fallback": {
                    "src": "/static/402ec135e08c3b799c16c08a82ae2dd8/68193/christopher-ayme-ocZ-_Y7-Ptg-unsplash.jpg",
                    "srcSet": "/static/402ec135e08c3b799c16c08a82ae2dd8/86d57/christopher-ayme-ocZ-_Y7-Ptg-unsplash.jpg 919w,\n/static/402ec135e08c3b799c16c08a82ae2dd8/075d8/christopher-ayme-ocZ-_Y7-Ptg-unsplash.jpg 1839w,\n/static/402ec135e08c3b799c16c08a82ae2dd8/68193/christopher-ayme-ocZ-_Y7-Ptg-unsplash.jpg 3677w",
                    "sizes": "(min-width: 3677px) 3677px, 100vw"
                  },
                  "sources": [
                    {
                      "srcSet": "/static/402ec135e08c3b799c16c08a82ae2dd8/6b4aa/christopher-ayme-ocZ-_Y7-Ptg-unsplash.webp 919w,\n/static/402ec135e08c3b799c16c08a82ae2dd8/0fe0b/christopher-ayme-ocZ-_Y7-Ptg-unsplash.webp 1839w,\n/static/402ec135e08c3b799c16c08a82ae2dd8/5d6d7/christopher-ayme-ocZ-_Y7-Ptg-unsplash.webp 3677w",
                      "type": "image/webp",
                      "sizes": "(min-width: 3677px) 3677px, 100vw"
                    }
                  ]
                },
                "width": 3677,
                "height": 2456
              }
            }
          ]
        }
      }
    }
  },
  "extensions": {}
}
```

`hero_image.childImageSharp` 필드의 `gatsbyImageData` 객체를 자세히 보면, 게시물의 히어로 이미지에 대한 많은 정보를 포함하고 있음을 확인할 수 있다. 이는 크기가 다른 이미지의 파일 경로, 이미지를 불러오는 동안 자리 표시자로 사용할 대체 이미지이다. `gatsby-plugin-sharp`이 빌드 시간에 해당 데이터를 모두 계산한다. 받은 응답의 `gatsbyImageData` 객체는 `GatsbyImage` 컴포넌트가 이미지를 렌더링하기위해 필요한 것과 구조가 동일하다.

> **Note:** GraphiQL에서 `gatsbyImageData` 필드가 `aspectRatio`, `formats`, `width`같은 여러 인자에 접근하는 것을 보았을 수도 있다. 이 인자를 사용하여 이미지 처리 라이브러인 Sharp에, 최적화된 이미지를 생성할 때 필요한 데이터를 전달할 수 있다.(번역 수정 필요[https://www.gatsbyjs.com/docs/tutorial/part-7/#render-hero-image-in-the-blog-post-page-template:~:text=You%20can%20use%20these%20arguments%20to%20pass%20in%20extra%20data%20about%20how%20you%20want%20the%20Sharp%20image%20processing%20library%20to%20create%20your%20optimized%20images.])
>
> 이러한 옵션은 `StaticImage` 컴포넌트에 props로 전달했던 것과 동등하다.
>
> 추가 정보는 [`gatsby-plugin-image` Reference Guide](https://www.gatsbyjs.com/docs/reference/built-in-components/gatsby-plugin-image/#image-options)에서 확인할 수 있다.

<br>

### 작업: `GatsbyImage` 컴포넌트 사용하여 히어로 이미지 추가

GraphQL 쿼리를 설정하고 나면, 이를 블로그 게시물 페이지 템플릿에 추가할 수 있다.

1. Replace your existing page query with the query you built in GraphiQL that includes the hero image frontmatter fields. 기존 페이지 쿼리 대신GraphiQL에서 생성한, frontmatter의 hero image 필드를 포함하는 쿼리를 입력한다.

   ```js:title=src/pages/blog/{mdx.frontmatter__slug}.js
   // imports

   const BlogPost = ({ data, children }) => {
     return (
       // ...
     )
   }

   export const query = graphql`
     query($id: String) {
       mdx(id: {eq: $id}) {
         frontmatter {
           title
           date(formatString: "MMMM DD, YYYY")
           hero_image_alt
           hero_image_credit_link
           hero_image_credit_text
           hero_image {
             childImageSharp {
               gatsbyImageData
             }
           }
         }
       }
     }
   `

   export const Head = ({ data }) => <Seo title={data.mdx.frontmatter.title} />

   export default BlogPost
   ```

2. `gatsby-plugin-image` 의 `GatsbyImage` 컴포넌트와 `getImage` 헬퍼 함수를 import한다.

```js:title=src/pages/blog/{mdx.frontmatter_slug}.js
import * as React from 'react'
import { graphql } from 'gatsby'
import { GatsbyImage, getImage } from 'gatsby-plugin-image'
import Layout from '../../components/layout'
import Seo from '../../components/seo'

// ...
```

3. `getImage` 헬퍼 함수를 사용하여 `hero_image` 필드의 `gatsbyImageData` 객체를 가져온다.

```js:title=src/pages/blog/{mdx.frontmatter__slug}.js
// imports

const BlogPost = ({ data, children }) => {
  const image = getImage(data.mdx.frontmatter.hero_image)

  return (
    // ...
  )
}

// ...
```

> **Note:** `getImage`는 `File`노드 또는 `ImageSharp` 노드를 가져와 이 노드에 대한 `gatsbyImageData` 객체를 반환하는 헬퍼 함수이다. `getImage`를 사용하여 코드를 좀 더 깔끔하고 읽기 쉽게 유지할 수 있다.
>
> `getImage` 헬퍼 함수가 없으면 대신 `data.mdx.frontmatter.hero_image.childImageSharp.gatsbyImageData`을 입력해야 한다.

<br>

4. `gatsby-plugin-image`의 `GatsbyImage` 컴포넌트를 사용하여 히어로 이미지 데이터를 렌더링한다. `GatsbyImage`에 prop 2개를 전달해야 한다.

   - `image`: `hero_image` 필드의 `gatsbyImageData` 객체
   - `alt`: `hero_image_alt` 필드에서 가져온, 이미지의 대체 텍스트

   ```js:title=src/pages/blog/{mdx.frontmatter__slug}.js
   return (
     <Layout pageTitle={data.mdx.frontmatter.title}>
       <p>Posted: {data.mdx.frontmatter.date}</p>
       <GatsbyImage
         image={image}
         alt={data.mdx.frontmatter.hero_image_alt}
       />
       {children}
     </Layout>
   )
   ```

<br>

5. 이제 블로그의 각 게시물 페이지를 방문하면 페이지에 상응하는 히어로 이미지가 게시물 본문 위에 렌더링된 모습을 볼 수 있다.

![A screenshot of the My First Post blog page, with a hero image of a gray pitbull relaxing on the sidewalk.](https://www.gatsbyjs.com/static/db0a95d0b713366aed2dae2b4312ad60/60b3a/blog-post-with-hero-image.png)
![A screenshot of the Another Post blog page, with a hero image of a gray and white pitbull in a swimming pool.](https://www.gatsbyjs.com/static/14792977737184870eb709b281eedc78/60b3a/another-post.png)
   ![A screenshot of the Yet Another Post blog page, with a hero image of a dog wearing googly-eye glasses.](https://www.gatsbyjs.com/static/4d67e4eb42156d3c0392d9755f4fae11/60b3a/yet-another-post.png)
   _Images by Gatsbyjs, from https://www.gatsbyjs.com/docs/tutorial/part-7/, licensed under [MIT](https://opensource.org/licenses/MIT)._

<br>

### 작업: 히어로 이미지 밑에 크레딧 추가

이미지를 사용할 때, 크레딧을 표기하여 저작자를 알려주는 것이 중요하다.

> **Pro Tip:** 크레딧 링크를 클릭하면 외부 페이지로 이동하기 때문에, (즉, 내 사이트가 아닌 다른 페이지로 이동하기 때문에) Gatsby의 `Link` 컴포넌트 대신에 HTML의 `<a>` 태그를 사용한다.
>
> Gatsby'의 `Link` 컴포넌트는 내 사이트 안에 있는 페이지 링크애 대해서만 성능 면의 이점을 제공한다는 점을 기억하자.

```js:title=src/pages/blog/{mdx.frontmatter__slug}.js
// imports

const BlogPost = ({ data, children }) => {
  const image = getImage(data.mdx.frontmatter.hero_image)

  return (
    <Layout pageTitle={data.mdx.frontmatter.title}>
      <p>{data.mdx.frontmatter.date}</p>
      <GatsbyImage
        image={image}
        alt={data.mdx.frontmatter.hero_image_alt}
      />
      <p>
        Photo Credit:{" "}
        <a href={data.mdx.frontmatter.hero_image_credit_link}>
          {data.mdx.frontmatter.hero_image_credit_text}
        </a>
      </p>
      {children}
    </Layout>
  )
  }

export const query = graphql`
  ...
`

export const Head = ({ data }) => <Seo title={data.mdx.frontmatter.title} />

export default BlogPost
```

<Announcement>

> **Syntax Hint:** `<p>` 태그의 텍스트를 보면, "Photo Credit:" 뒤에 `{" "}`이 있다. 이는 콜론(`:`)과 링크 사이에 공백도 렌더링하기 위해 넣은 것이다.
> 
> `{" "}`을 제거하면 단락 텍스트로 "Photo Credit:Author"이 들어가게 된다.

<br>

![A screenshot of the My First Post blog page, which now includes a photo credit underneath the hero image. It says, "Photo Credit: Christopher Ayme".](https://www.gatsbyjs.com/static/8cc2270946e50f9cca852ac956c7634b/60b3a/blog-post-with-hero-image-credit.png)
_Image by Gatsbyjs, from https://www.gatsbyjs.com/docs/tutorial/part-7/, licensed under [MIT](https://opensource.org/licenses/MIT)._

<br>

> 개츠비가 제공하는 예제 사이트는 (https://github.com/gatsbyjs/tutorial-example)에서 확인할 수 있다.

<br>

## 요약

복습용 질문

- `StaticImage` 대신 `GatsbyImage` 컴포넌트를 사용해야 하는 경우는?

<br>

**Ship It!** 🚀

사이트 변경 사항을 개츠비 클라우드에 배포하여 진행 경과를 공유할 수 있다.

먼저 터미널에서 다음 명령을 실행하여 GitHub 리포지터리에 변경 사항을 push한다. (개츠비 사이트의 최상위 디렉토리에 있는지 확인해야 한다!)

> ```bash
> git add .
> git commit -m "Finished Gatsby Tutorial Part 6"
> git push
> ```
>
> 변경 사항이 GitHub에 push되면 Gatsby Cloud가 업데이트를 인식한 후 사이트 최신 버전을 다시 빌드 및 배포한다. (변경한 부분이 사이트에 반영되기까지 몇 분 정도 걸린다. 빌드 진행 과정은 [Gatsby Cloud dashboard](https://www.gatsbyjs.com/dashboard/)에서 확인할 수 있다.)

<br>

### 핵심 내용

- 컴포넌트가 (상대 경로 또는 원격 URL로부터) 항상 동일한 이미지를 렌더링하는 경우 `StaticImage` 구성 요소를 사용한다.
- 컴포넌트의 인스턴스가 각기 달라서 이미지 소스가 변경되는 경우 (예: 이미지 소스가 prop으로 전달되는 경우) `GatsbyImage` 컴포넌트를 사용한다.

<br>

## 튜토리얼 완료

[더 알아보기](https://www.gatsbyjs.com/docs/tutorial/whats-next/)
