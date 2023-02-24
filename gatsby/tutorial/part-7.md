# Part 7: 데이터로부터 동적 이미지 추가

<details><summary>목차</summary>
<p>

[소개](#소개)

[`GatsbyImage`와 `StaticImage`의 차이점](#gatsbyimage와-staticimage의-차이점)

[블로그 게시물 frontmatter에 히어로 이미지(Hero Image) 추가](#블로그-게시물-frontmatter에-히어로-이미지-추가)

[`gatsby-transformer-sharp` 설치 및 구성(configuration)](#gatsby-transformer-sharp-설치-및-구성configuration)

[블로그 게시물 페이지 템플릿에 히어로 이미지 렌더링](#블로그-게시물-페이지-템플릿에-히어로-이미지-렌더링)

- [작업: GraphiQL 사용하여 쿼리 빌드](#작업-graphiql-사용하여-쿼리-빌드)
- [작업: `GatsbyImage` 컴포넌트 사용하여 히어로 이미지 추가](#작업-gatsbyimage-컴포넌트-사용하여-히어로-이미지-추가)
- [작업: 히어로 이미지 아래에 크레딧 추가](#작업-히어로-이미지-아래에-크레딧-추가)

[요약](#요약)

- [핵심 내용](#핵심-내용)
- [개츠비 튜토리얼 완료](#개츠비-튜토리얼-완료)

</p>
</details>

## 소개

[Part 3](/docs/tutorial/part-3/)에서 `gatsby-plugin-image`를 사용하여 Home 페이지에 정적 이미지를 추가했었다. 개츠비의 데이터 레이어로 작업을 조금 해 보았으니, 이제 `gatsby-plugin-image`얘기를 다시 할 차례이다. 이번에는 사이트에 동적 이미지를 추가하는 방법을 배울 것이다.

이번 장에서는 동적 `GatsbyImage` 컴포넌트를 사용하여 블로그 게시물 각각에 히어로 이미지(hero image)를 추가해 본다.

이 장을 마치고 나면 다음의 작업을 할 수 있게 된다.

- `GatsbyImage` 컴포넌트를 사용하여 데이터로부터 동적 이미지 생성

<br>

## `GatsbyImage`와 `StaticImage`의 차이점

[Part 3](/docs/tutorial/part-3/)에서는 `gatsby-plugin-image`으로부터 `StaticImage` 컴포넌트를 사용하는 방법을 설명한다.

`StaticImage` 컴포넌트를 사용해야할 까, `GatsbyImage` 컴포넌트를 사용해야할 까. 어떻게 알 수 있을까? 이는 결국 컴포넌트가 렌더링되는 모든 시기마다 동일한 이미지 소스를 사용할 지에 대한 문제이다.

- `StaticImage`는 _static(정적)_ 이미지 소스를 위한 컴포넌트이다. 하드 코딩으로 작성한 파일 path나 원격 URL처럼 말이다. 즉, 이 컴포넌트를 렌더링할 때마다 이미지 소스는 항상 동일하다.
- `GatsbyImage`는 _dynamic(동적)_ 이미지 소스를 위한 컴포넌트이다. 이 경우에 이미지 소스는 prop 형태로 전달된다.

<br>

둘의 차이점을 이해하기 쉽게 비유해서 설명하자면 다음과 같다.

- `StaticImage` 컴포넌트 사용은, 물리 주소(physical address) "400 메인 스트리트"를 말하며 방향을 묻는 것과 같다. 얼마나 많은 사람에게 물었는지와 상관없이 결국엔 같은 장소에 도착한다.
- `GatsbyImage` 컴포넌트 사용은, 보다 포괄적으로 방향을 묻는 것과 같다. 누군가에게 마을 내 최고의 커피숍을 묻는다면 결국 누구에게 물었는지와 그 사람의 개인적인 선호가 무엇인지에 따라 결과가 달라진다.

![A side-by-side of two identical cartoon maps of a little town. The first, labeled "StaticImage", labels one of the houses "400 Main Street". The second, labeled "GatsbyImage", labels three of the houses with "coffee shop".](https://www.gatsbyjs.com/static/c6fccf3d19b405ef92dd65472a6fb8f4/a5262/staticimage-vs-gatsbyimage-towns.png)

<br>

이번 장에서는 블로그 게시물 페이지 템플릿에 히어로 이미지를 추가할 것이다. 또한 각 게시물마다 사용할 이미지를 명시하기 위해 블로그 게시물 `.mdx` 파일에 frontmatter 데이터를 추가할 것이다. 페이지 템플릿에 로드하는 이미지 소스가 게시물마다 다르기 때문에 `GatsbyImage` 컴포넌트를 사용한다.

<br>

## 블로그 게시물 frontmatter에 히어로 이미지(Hero Image) 추가

많은 블로그 사이트에서 게시물 상단에 히어로 이미지를 사용하고 있다. 이런 이미지는 대개 크고 고품질인 사진으로, 독자의 시선을 사로잡아 해당 페이지에 더 오래 머물고 싶게끔 한다.

아래 단계를 통해 히어로 이미지로 적합한 사진을 찾아 다운 받을 수 있으며, 블로그 게시물의 frontmatter에 추가할 수 있다.

<br>

1. 모든 MDX 게시물로 `blog` 디렉토리를 구성하면서 시작한다. 먼저 `blog` 폴더 안에 각 게시물에 대한 하위 디렉토리를 생성한다. 그 다음 각 `.mdx` 파일의 이름을 `index.mdx`로 변경한다. (route가 `blog/my-post/my-post/`같은 중복 path parameter를 갖게 되는 상황을 막기 위함)
   - 예를 들면 `blog/my-first-post.mdx`의 게시물이 `blog/my-first-post/index.mdx`로 이동한다. 마찬가지로 `blog/another-post.mdx`의 게시물이 `blog/another-post/index.mdx`로 이동한다.

> **Note:** .mdx`파일의 위치나 이름을 바꾼 후, 변경 사항이 적용되도록 로컬 개발 서버를 중단하고 다시 시작해야 한다.

![Diagram of moving each MDX file into its own subdirectory in the blog folder](https://www.gatsbyjs.com/static/f7150a9cc0e9f60b6d51c544ecc79117/1816f/reorganize-blog-directory.png)

2. [Unsplash](https://unsplash.com) 같은 웹사이트를 사용하여 자유롭게 사용할 수 있는 예쁜 이미지를 찾는다. 화면에 잘 맞도록 가로 방향 이미지를 선택한다.
   ![A screenshot of the Unsplash home page](https://www.gatsbyjs.com/static/c8cfa84caf6e939eb724737f03e6943f/60b3a/unsplash.png)

3. 마음에 드는 사진을 찾았으면 다운 받아 이를 특정 게시물에 대한 하위 디렉토리에 추가한다.
   ![Diagram of the "blog" folder structure, with the hero image for each post in the subdirectory for that post.](https://www.gatsbyjs.com/static/5cc60b117349ec93e6f1b59b34917a8e/60b3a/folder-structure-with-images.png)

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
// highlight-start
hero_image: "./christopher-ayme-ocZ-_Y7-Ptg-unsplash.jpg"
hero_image_alt: "A gray pitbull relaxing on the sidewalk with its tongue hanging out"
hero_image_credit_text: "Christopher Ayme"
hero_image_credit_link: "https://unsplash.com/photos/ocZ-_Y7-Ptg"
// highlight-end
---

...
```

```mdx:title=blog/another-post/index.mdx
---
title: "Another Post"
date: "2021-07-24"
slug: "another-post"
// highlight-start
hero_image: "./anthony-duran-eLUBGqKGdE4-unsplash.jpg"
hero_image_alt: "A grey and white pitbull wading happily in a pool"
hero_image_credit_text: "Anthony Duran"
hero_image_credit_link: "https://unsplash.com/photos/eLUBGqKGdE4"
// highlight-end
---

...
```

```mdx:title=blog/yet-another-post/index.mdx
---
title: "Yet Another Post"
date: "2021-07-25"
slug: "yet-another-post"
// highlight-start
hero_image: "./jane-almon-7rriIaBH6JY-unsplash.jpg"
hero_image_alt: "A white pitbull wearing big googly-eye glasses"
hero_image_credit_text: "Jane Almon"
hero_image_credit_link: "https://unsplash.com/photos/7rriIaBH6JY"
// highlight-end
---

...
```

이미지가 준비되었으니, 블로그 게시물 페이지 템플릿에 히어로 이미지를 pull할 수 있도록 이를 데이터 레이어에 연결한다.

<br>

## `gatsby-transformer-sharp` 설치 및 구성(configuration)

`GatsbyImage` 컴포넌트를 사용하기 위해, `gatsby-transformer-sharp` transformer plugin을 사이트에 추가한다.

Gatsby가 빌드 시간에 노드를 데이터 레이어에 추가할 때, `gatsby-transformer-sharp` plugin은 `.png`, `.jpg` 같은 이미지 확장자를 갖는 모든 `File` 노드를 찾는다. 그리고 이 파일에 대한 `ImageSharp` 노드를 생성한다.

![A diagram showing how ImageSharp nodes get created from File nodes in the data layer](https://www.gatsbyjs.com/static/d710adc16d08d60f8919c62468ec0e8f/a5262/data-layer-with-imagesharp-nodes.png)

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
       "gatsby-transformer-sharp", // highlight-line
     ],
   }
   ```

`gatsby-transformer-sharp`을 사이트에 추가했기 때문에, GraphiQL에서 변경사항을 확인하기 위해 로컬 개발 서버를 중단한 후 다시 시작해야 한다. 다음 단계에서 GraphiQL을 더 자세히 살펴볼 것이다.

<br>

## 블로그 게시물 페이지 템플릿에 히어로 이미지 렌더링

필수 도구들이 준비되었으니 이제 히어로 이미지를 블로그 게시물 페이지 템플릿에 추가할 수 있다.

### 작업: GraphiQL 사용하여 쿼리 빌드

먼저, GraphiQL을 사용하여 히어로 이미지 관련 frontmatter 필드를 블로그 게시물 페이지 템플릿의 GraphQL 쿼리에 추가한다.

1. Open GraphiQL by going to 웹 브라우저에서 `localhost:8000/___graphql`을 방문하여 GraphiQL 연다. 현재 블로그 게시물 페이지 쿼리를 GrpahiQL 쿼리 편집창에 복사하면서 시작한다. 한 번 실행해서 모든 작업이 제대로 이루어지는 지 확인한다.

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

2. 탐색 창에서 In the Explorer pane, check the boxes for the `hero_image_alt`, `hero_image_credit_link`, `hero_image_credit_text` 필드를 선택한다. 쿼리를 실행하면 아래의 JSON 객체와 같은 응답을 받는다.

> **Note:** 위 필드는 `frontmatter:` 인자(옥색) 말고, 아래에 있는 `frontmatter` 필드(파란색) 목록에서 확인할 수 있다.

```graphql
query ($id: String) {
  mdx(id: {eq: $id}) {
    frontmatter {
      title
      date(formatString: "MMMM D, YYYY")
      // highlight-start
      hero_image_alt
      hero_image_credit_link
      hero_image_credit_text
      // highlight-end
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
      // highlight-start
      hero_image {
        childImageSharp {
          gatsbyImageData
        }
      }
      // highlight-end
    }
  }
}
```

<Announcement>

**Pro Tip:** How does GraphiQL know to add extra fields to the `hero_image` frontmatter field?

When Gatsby builds your site, it creates a GraphQL **schema** that describes the different types of data in the data layer. As Gatsby builds that schema, it tries to guess the type of data for each field. This process is called **schema inference**.

Gatsby can tell that the `hero_image` field from your MDX frontmatter matches a `File` node, so it lets you query the `File` fields for that node. Similarly, `gatsby-transformer-sharp` can tell that the file is an image, so it also lets you query the `ImageSharp` fields for that node.

</Announcement>

4. Run your query to see what data you get back in the response. It should mostly look like the response you got back before, but this time with an extra `hero_image` object:

```json
{
  "data": {
    "mdx": {
      "frontmatter": {
        // ...
        // highlight-start
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
        // highlight-end
      }
    }
  },
  "extensions": {}
}
```

If you take a closer look at the `gatsbyImageData` object on the `hero_image.childImageSharp` field, you'll see that it contains a bunch of information about the hero image for that post: dimensions, file paths for the images at different sizes, fallback images to use as a placeholder while the image loads. All this data gets calculated by `gatsby-plugin-sharp` at build time. The `gatsbyImageData` object in your response has the same structure that the `GatsbyImage` component needs to render an image.

<Announcement>

**Note:** You might have noticed that the `gatsbyImageData` field in GraphiQL accepts several arguments, like `aspectRatio`, `formats`, or `width`. You can use these arguments to pass in extra data about how you want the Sharp image processing library to create your optimized images.

These options are equivalent to the ones you would pass into the `StaticImage` component as props.

For more information, see the [`gatsby-plugin-image` Reference Guide](/docs/reference/built-in-components/gatsby-plugin-image/#image-options).

</Announcement>

### 작업: `GatsbyImage` 컴포넌트 사용하여 히어로 이미지 추가

Once you have your GraphQL query set up, you can add it to your blog post page template.

1. Replace your existing page query with the query you built in GraphiQL that includes the hero image frontmatter fields.

   ```js:title=src/pages/blog/{mdx.frontmatter__slug}.js
   // imports

   const BlogPost = ({ data, children }) => {
     return (
       // ...
     )
   }

   // highlight-start
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
   // highlight-end

   export const Head = ({ data }) => <Seo title={data.mdx.frontmatter.title} />

   export default BlogPost
   ```

2. Import the `GatsbyImage` component and the `getImage` helper function from the `gatsby-plugin-image` package.

```js:title=src/pages/blog/{mdx.frontmatter_slug}.js
import * as React from 'react'
import { graphql } from 'gatsby'
import { GatsbyImage, getImage } from 'gatsby-plugin-image' // highlight-line
import Layout from '../../components/layout'
import Seo from '../../components/seo'

// ...
```

2. Use the `getImage` helper function to get back the `gatsbyImageData` object from the `hero_image` field.

```js:title=src/pages/blog/{mdx.frontmatter__slug}.js
// imports

const BlogPost = ({ data, children }) => {
  const image = getImage(data.mdx.frontmatter.hero_image) // highlight-line

  return (
    // ...
  )
}

// ...
```

<Announcement>

**Note:** `getImage` is a helper function that takes in a `File` node or an `ImageSharp` node and returns the `gatsbyImageData` object for that node. You can use it to keep your code a little cleaner and easier to read.

Without the `getImage` helper function, you'd have to type out `data.mdx.frontmatter.hero_image.childImageSharp.gatsbyImageData` (which is longer, but gives you back the same data).

</Announcement>

3. Use the `GatsbyImage` component from `gatsby-plugin-image` to render the hero image data. You should pass `GatsbyImage` two props:

   - `image`: the `gatsbyImageData` object for your `hero_image` field
   - `alt`: the alternative text for your image, from the `hero_image_alt` field

   ```js:title=src/pages/blog/{mdx.frontmatter__slug}.js
   return (
     <Layout pageTitle={data.mdx.frontmatter.title}>
       <p>Posted: {data.mdx.frontmatter.date}</p>
       {/* highlight-start */}
       <GatsbyImage
         image={image}
         alt={data.mdx.frontmatter.hero_image_alt}
       />
       {/* highlight-end */}
       {children}
     </Layout>
   )
   ```

4. Now, when you visit each of your blog post pages, you should see the corresponding hero image rendered before the body of your post!
   ![A screenshot of the My First Post blog page, with a hero image of a gray pitbull relaxing on the sidewalk.](./blog-post-with-hero-image.png)

   ![A screenshot of the Another Post blog page, with a hero image of a gray and white pitbull in a swimming pool.](./another-post.png)

   ![A screenshot of the Yet Another Post blog page, with a hero image of a dog wearing googly-eye glasses.](./yet-another-post.png)

### 작업: 히어로 이미지 아래에 크레딧 추가

It's important to give credit to people whose work you use in your own site. The last piece of including hero images to your site is to add a paragraph to give credit to the photographer.

<Announcement>

**Pro Tip:** Since the credit link goes to an external page (in other words, one that's not part of your site), you can use the `<a>` HTML tag instead of the Gatsby `Link` component.

Remember, Gatsby's `Link` component only gives performance benefits for internal links to other pages within your site.

</Announcement>

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
      {/* highlight-start */}
      <p>
        Photo Credit:{" "}
        <a href={data.mdx.frontmatter.hero_image_credit_link}>
          {data.mdx.frontmatter.hero_image_credit_text}
        </a>
      </p>
      {/* highlight-end */}
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

**Syntax Hint:** You might have noticed that there's a `{" "}` after the "Photo Credit:" text `<p>` tag. That's to make sure that a space gets rendered between the colon (`:`) and the link text.

Try removing the `{" "}` and see what happens. The paragraph text should end up being "Photo Credit:Author".

</Announcement>

![A screenshot of the My First Post blog page, which now includes a photo credit underneath the hero image. It says, "Photo Credit: Christopher Ayme".](./blog-post-with-hero-image-credit.png)

<Announcement>

**Want to see how it all fits together?** Check out the finished state of the [GitHub repo for the example site](https://github.com/gatsbyjs/tutorial-example).

</Announcement>

## 요약

Take a moment to think back on what you've learned so far. Challenge yourself to answer the following questions from memory:

- When should you use the `GatsbyImage` component instead of the `StaticImage` component?

<Announcement>

**Ship It!** 🚀

Before you move on, deploy your changes to your live site on Gatsby Cloud so that you can share your progress!

First, run the following commands in a terminal to push your changes to your GitHub repository. (Make sure you're in the top-level directory for your Gatsby site!)

```shell
git add .
git commit -m "Finished Gatsby Tutorial Part 7"
git push
```

Once your changes have been pushed to GitHub, Gatsby Cloud should notice the update and rebuild and deploy the latest version of your site. (It may take a few minutes for your changes to be reflected on the live site. Watch your build's progress from your [Gatsby Cloud dashboard](/dashboard/).)

</Announcement>

### 핵심 내용

- Use the `StaticImage` component if your component always renders the same image (from a relative path or a remote URL).
- Use the `GatsbyImage` component if the image source changes for different instances of your component (like if it gets passed in as a prop).

<Announcement>

**Share Your Feedback!**

Our goal is for this Tutorial to be helpful and easy to follow. We'd love to hear your feedback about what you liked or didn't like about this part of the Tutorial.

Use the "Was this doc helpful to you?" form at the bottom of this page to let us know what worked well and what we can improve.

</Announcement>

### 개츠비 튜토리얼 완료

Congratulations, you've reached the end of the official Gatsby Tutorial! 🥳

Want to know more? The next page includes some additional resources that you can use to continue learning about Gatsby.

<LinkButton
to="/docs/tutorial/whats-next/"
rightIcon={<MdArrowForward />}
variant="SECONDARY"

> Continue to What's Next
> </LinkButton>
