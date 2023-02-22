# 리액트 네이티브 관련 메모

## 2023. 01. 14.

### Stack Navigator

- 앱 내 화면 전환 지원
- 새로운 화면은 스택의 맨 위에 놓임
- iOS와 안드로이드의 [룩앤필](https://ko.wikipedia.org/wiki/%EB%A3%A9_%EC%95%A4%EB%93%9C_%ED%95%84)을 따름<br>

### 앱 내용 외부로 공유하는 기능

```
const share = () => {
    Share.share({
        message:`${tip.title} \n\n ${tip.desc} \n\n ${tip.image}`,
    });
}
```

> 공유할 데이터는 message 키에 준비. Share 도구 꺼냈으면 그 안에 share 함수를 넣어야 한다.<br>

궁금한 점: 이미지 주소(tip.image)를 전달하면 카톡 공유 시 카톡이 알아서 썸네일을 보여준다고 하던데, 실제 전송 시 썸네일이 보이지 않았다.
