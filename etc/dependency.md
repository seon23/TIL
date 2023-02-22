# dependency
> 이 글은 [Mozilla 기여자](https://developer.mozilla.org/en-US/docs/MDN/Community/Roles_teams#contributor)가 작성한 ["Package management basics"](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Understanding_client-side_tools/Package_management) 일부를 재가공한 것입니다. 원본에는 [CC-BY-SA 2.5](https://creativecommons.org/licenses/by-sa/2.5/) 라이선스가 적용되어 있으며, 해당 규약을 따라 이 글에도 동일한 라이선스가 적용됩니다.

<br>

**dependency**란?
- 누군가 작성해 놓은 <u>third-party 소프트웨어</u>로, 이를 통해 특정 문제를 해결할 수 있다. 웹 프로젝트에는 dependency가 여러 개 있을 수 있으며, dependency에는 직접 명시해서 설치하지 않았던 sub-dependency가 포함될 수도 있다.

dependency의 유용함
- 상대 날짜를 사람이 읽을 수 있는 날짜 형태로 계산하는 코드를 생각해 보자. 직접 이 코드를 작성할 수도 있지만, 누군가 이미 해결했다면 이를 이용하면 된다. 
- 신뢰할 만한 third-party dependency는 여러 다양한 상황에서 테스트를 마쳤을 가능성이 높다. 이를 통해 dependency는 직접 만든 것보다 안정성, 크로스 브라우징 면에서 더 나은 성능을 보인다.

프로젝트에서 사용하는 dependency
- 프로젝트 dependency는 <u>React, Vue</u>와 같은 자바스크립트 라이브러리나 프레임워크 전체일 수도 있다. 또는 사람이 읽을 수 있는 날짜를 계산하는 라이브러리처럼 매우 작은 유틸리티일 수도, <u>Prettier이나 ESLint</u>와 같은 커맨드라인 툴일 수도 있다.

dependency 설치하는 모던 빌드 툴
- 일단 `<script>` 엘리먼트를 통해 dependency를 프로젝트에 포함할 수 있다. 그러나 이 방식으로는 dependency를 즉시 사용하지 못할 수도 있다. 코드와 dependency를 웹에 release할 때 bundle하는 모던 툴이 필요하다. 
- <u>bundle</u>은 웹 서버 상의 단일 파일을 지칭할 때 일반적으로 쓰는 용어인데, 여기 파일에는 소프트웨어를 구성하는 모든 자바스크립트가 들어 있다. 이 파일을 최대한 많이 압축하곤 하는데, 이는 소프트웨어가 다운로드되어 방문자 브라우저에 보이는 단축하는 데에 도움을 주기 위함이다.
- npm같은 **패키지 매니저**를 사용하면 대규모 프로젝트에서도 dependency를 깔끔하게 추가, 제거할 수 있을 뿐만 아니라 다른 많은 이점도 얻을 수 있다. 
