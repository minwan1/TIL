## Spring


@Consumes : 수신 하고자하는 데이터 포맷을 정의한다.(request)
@Produces : 출력하고자 하는 데이터 포맷을 정의한다.(response)

Produces와 Consumes 차이 : @Produces는 Accept 헤더를 참고하고, @Consumes은 Content-Type 헤더를 참고함

@RequestMapping
컨트롤러에 기본이 되는 요청정보를 설정하는 어노테이션이다.
Accept 와 content-Type 에 대한 접근 허용을 사용하기 위해 header 라는 속성을 사용했다면, 3.1부터는 consumes 와 produces 로 나눠 사용할 수 있다.


## spring 의 컨트롤러에서 gson의 JsonObject를 리턴하면 에러나는이유.
Spring 에서 @responsebody을 사용하게되면 RequestResponseBodyMethodProcessor 는 MappingJackson2HttpMessageConverter 이핸들러를 사용하여 serialize한다. 내부적으로 MappingJackson2HttpMessageConverter는 ObjectMapper을 사용한다. 기본적으로 ObjectMapper는 클래스의 getter을 이용하여 serialize하여 json형식으로 만든다.
그래서 gson은 출력안되고 JSONObject는 json형식으로 출력해주는듯.
참고

## javascript

이렇게 contentType을 application/json으로 명시해줄 경우 반드시 Stringify를 해야 한다. 그렇지 않으면, 기본적으로 Ajax는 data값이 string이 아닐 경우 pre-process과정을 통해 data obejct를 query String형태(?key=value&key=value&…)로 변경시킨다. 결국 서버에서는 JSON형태가 아니기에 잘못된 파라미터로 인식하고 400 error(Bad request)를 리턴 할 것이다.

* [Syaku 샤쿠](http://syaku.tistory.com/277)
* [참고](https://stackoverflow.com/questions/19204048/returning-jsonobject-using-responsebody-in-springmvc)
