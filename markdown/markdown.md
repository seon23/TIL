# 마크다운 문법
[Markdown Guide](https://www.markdownguide.org/) 사이트 참고

## [줄바꿈](https://www.markdownguide.org/basic-syntax/#line-breaks)
스페이스바를 두 번 이상 누른 다음 엔터를 치면 되는데, 편집기가 이 후행 공백을 발견하기 어려울 수 있다.  
마크다운 편집기가 HTML을 지원한다면 \<br>로 대체하는 편이 낫다.

<br>

## [하이라이트](https://www.markdownguide.org/extended-syntax/#highlight)
일부 마크다운 편집기에서는 하이라이팅 기능을 제공한다.(VSCode에서는 미제공)<br>
강조하고 싶은 부분을 `==`로 감싸면 된다.<br>
```
일부 마크다운 처리기에서는 ==하이라이팅 기능==을 제공한다.
```

HTML을 지원한다면 `<mark></mark>` 태그를 사용한다.
```
일부 마크다운 처리기에서는 <mark>하이라이팅 기능</mark>을 제공한다.
```

<br>

## [이미지 삽입](https://www.markdownguide.org/basic-syntax/#images-1)
```
![대체 텍스트](이미지 파일 경로 or URL "이미지 타이틀 - 옵션")
```

### [이미지 크기 변경](https://www.markdownguide.org/hacks/#image-size)
이미지의 width, height를 지정하는 마크다운 구문은 없다. 대신 마크다운 편집기가 HTML을 지원한다면 HTML 태그를 사용해서 width, height를 정해줄 수 있다.
```
<img src="image.png" width="200" height="100">
```


