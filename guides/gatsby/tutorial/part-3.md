# Part 3: Add Features with Plugins

<details><summary>목차</summary>
<p>

[plugin이란?](#plugin이란)

[사이트에 plugin 추가](#사이트에-plugin-추가)

[Task: 홈페이지에 static image 넣기(`gastby-plugin-image`)](#task-gastby-plugin-image를-사용해-홈페이지에-static-image-넣기)

</p>
</details>


## plugin이란?
개츠비에서 **plugin**은 사이트 부가 기능을 추가하기 위해 설치하는 npm 패키지를 말한다.

## 사이트에 plugin 추가
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

## Task: `gastby-plugin-image`를 사용해 홈페이지에 static image 넣기
해당 플러그인은 다른 플러그인을 필요로 하는데, 관련 설명은 [여기](https://www.gatsbyjs.com/docs/tutorial/part-3/#introduction:~:text=The%20StaticImage%20component%20requires,install%20it%20for%20now.)에서 확인 가능하다.