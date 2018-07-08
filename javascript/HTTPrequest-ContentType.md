
## Content type 종류

Text타입으로는 text/css, text/javascript, text/html, text/plain등이 있다.

html문서에 type을 명시할 때 text/javascript 혹은 text/css를 이미 써봤으리라 생각한다.

File을 실어보내기 위한 타입으로는 multipart/formed-data가 있다.

그리고 Application 타입으로 application/json, application/x-www-form-urlencode가 있다.




서버 코드의 Controller에 다음의 request handler mapping이 정의되어 있다.
```ajva
@RequestMapping(value = "/test", method = RequestMethod.POST)

@ResponseBody

public String test(HttpServletRequest request) {

	String reqData = request.getParameter("data");

	return "success";

}
```
위에 작성되어 있는 handler가 정상 동작하는지에 대해서 테스트 코드를 작성한 후 HTTP POST 요청을 날렸다.

서버에서 요청은 잘 받지만 request.getParameter("data") 의 값은 null이 찍힌다.

이상한 것은 다음의 html 코드를 생성한 후 브라우저에서 호출을 하면 request.getParameter("data") 의 값이 "11111111"로 찍히는 것이다.

```html
<body onload="document.test.submit();">

<form method="POST" name="test" action="http://localhost:8080/test">

	<input type="hidden" name="data" id="data" value="11111111">

</form>

</body>
```

테스트 코드를 이용한 HTTP request 요청 시의 Content-Type은 text/plain 이였고, 브라우저를 이용한 테스트 시에는 application/x-www-form-urlencoded 를 서버에 전달했다.

---

application/x-www-form-urlencoded 컨텐츠 타입에 대해서 알아보면 key=value&key=value 와 같은 데이터를 전달한다라고 규약이 되어 있다.

즉, 서블릿 컨테이너는 컨텐츠 타입이 application/x-www-form-urlencoded 이면 request의 body를 읽어 Map 형태로 변환한다. 그렇게 때문에 request.getParameter 와 같은 형태로 데이터를 읽어 들일 수 있는 것이다.



테스트 코드를 이용한 방식으로의 요청 시 왜 서버 코드의 getParameter("data")로 데이터 추출이 불가능한가 싶어 네트워크 캡쳐까지 해봤는데 정상적으로 HTTP request body에 데이터를 실어서 전송하고 있다.

이상한 생각에 body데이터를 stream을 이용해 읽어 보니 body에는 데이터가 존재한다.

String reqData = HttpUtil.readStream(request.getInputStream());



---

application/x-www-form-urlencoded 헤더 타입이면서 HTTP POST 요청 시

 -> getParameter("data") 와 같은 방법으로 추출 가능



text/plain 헤더 타입이면서 HTTP POST 요청 시

 -> getParameter("data") 와 같은 방법으로 데이터 추출 불가능

 -> request body를 stream을 이용하여 읽어와야 함.


[탁구치는 개발자](http://lng1982.tistory.com/225)
