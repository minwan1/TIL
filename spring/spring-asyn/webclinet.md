# WebClinet

## 웹클라이언트 생성

간단하게 웹클라이언트를 만들 수 있다.
```java
WebClient webClient = WebClient.create();
// 특정 URL을 지정하려면 아래와같이 사용할 수 있따.
WebClient webClient = WebClient.create("https://api.github.com");
다음과 같이 빌더를 사용하는 방법 또한 있다.

WebClient webClient = WebClient.builder()
        .baseUrl("https://api.github.com")
        .defaultHeader(HttpHeaders.CONTENT_TYPE, "application/vnd.github.v3+json")
        .defaultHeader(HttpHeaders.USER_AGENT, "Spring 5 WebClient")
        .build();

```
