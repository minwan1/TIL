# HandlerMethodArgumentResolver 인터페이스를 이용한 Controller 커스텀 파라미터 객체 만들기
이 인스터페이스는 전략 패턴의 일종으로 컨트롤러 메서드에서 특정 조건에 해당하는 파라미터에 바인딩 해주는 전략 인터페이스이다. 따라서 AOP로 모든 메서드를 일일이 찾아서 데이터를 바인딩 할 필요없이 어노테이션을 만들어 쉽게 바인딩 할 수 있다.

예를 들어 컨트롤러에서 세션을 조회해야 하거나 HttpServletRequest에서 토큰 또는 쿠키 정보를 기반으로 인증 객체를 만들어야 한다고 가정해보자. 그렇게 되면 이러한 객체를 만들기 위해서는 아래의 소스같이 session에서 유저정보를 가져오거나 request 정보에서 인증정보를 가져오는 중복 코드가 발생할 수 있다. 이러한 문제를 HandlerMethodArgumentResolver를 이용하면 쉽게 해결할 수 있다.

**HandlerMethodArgumentResolver 사용 안한 경우**

```java
@RestController
@RequestMapping("/members")
public class MemberController {

    @GetMapping
    public ResponseEntity<Member> getMember(){
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        User user = (User) authentication.getPrincipal();

        return new ResponseEntity<>(member, HttpStatus.OK);
    }

}
```

**HandlerMethodArgumentResolver 사용한경우**
HandlerMethodArgumentResolver 를 사용하면 위와 같이 컨트롤러마다 인증정보를 만들 필요 없이 아래와 같이 커스텀 어노테이션을 정의하고 HandlerMethodArgumentResolver를 동록 해놓으면 아래와 같이 유저정보를 바인딩 해서 사용할 수 있다.

```java
@RestController
@RequestMapping("/members")
public class MemberController {

    @GetMapping
    public ResponseEntity<Member> getMember(@MemberSession Member member){
        return new ResponseEntity<>(member, HttpStatus.OK);
    }

}
```

HandlerMethodArgumentResolver를 등록하기위해서는 아래의 HandlerMethodArgumentResolver 인터페이스를 구현해야한다.

```java
public interface HandlerMethodArgumentResolver {
    boolean supportsParameter(MethodParameter parameter);

    Object resolveArgument(MethodParameter parameter, ModelAndViewContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory binderFactory) throws Exception

}
```

HandlerMethodArgumentResolver 인터페이스는 다음 두 메서드를 제공한다.
* supportsParameter() 메서드 : HandlerMethodArgumentResolver가 해당하는 파라미터를 지원할지 여부를 반환한다. true를 반환하면 resolveArgument메서드가 수행된다.
* resolverArgument() 메서드 : 파라미터의 인자값에 대한 정보를 실제 객체를 생성하여 해당 파라미터로 객체에 바인딩 한다.


그리고 위에서 등록한 Resolver를 스프링 webmvc resolver에 등록해야 해당 resolver가 작동된다. 아래와 같이 resolver를 등록할 수 있다.
```java
@SpringBootApplication
public class SecurityApplication implements WebMvcConfigurer {


    @Autowired
    private UserArgumentResolver userArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(userArgumentResolver);
    }

    public static void main(String[] args) {
        SpringApplication.run(SecurityApplication.class, args);
    }

}


```

실제로 HandlerMethodArgumentResolver를 구현해 볼 것이다. 먼저 파라미터에 바인딩 되어질 어노테이션을 아래와 같이 선언해야한다.

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface MemberSession {
}
```

그리고 실제 HandlerMethodArgumentResolver 구현에서는 컨트롤러 메소드 파라미터에 MemberSession이 존재하고, 해당 parameter type이 Member 클래스이면 "test" member를 리턴하게 하고 그 결과값을 Rest API로 받아 볼것이다.

이것은 예제지만 실제 세션정보를 불러온다거나 HTTP Request에 정보를 바탕으로 유저 객체를 만드는 로직을 넣을 수도 있다.

```java
@Component
public class UserArgumentResolver implements HandlerMethodArgumentResolver {
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.getParameterAnnotation(MemberSession.class) != null && parameter.getParameterType().equals(Member.class);
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {

        //session 정보 조회 로직
        return new Member("test");
    }
}
```


이제 실제 예제 컨트롤러를 호출해보면 member 변수에 "test" 멤버 객체를 리턴하는 것을 확인할 수 있다. 아래는 해당 예제 소스이다. localhost:8080/members 를 호출해보면 결과값을 받아 볼 수 있다.


* [예제 소스](https://github.com/minwan1/blog-example/tree/master/argument-resolver)

참고
* [처음으로 배우는 스프링 부트 2](http://book.interpark.com/product/BookDisplay.do?_method=detail&sc.saNo=001&sc.prdNo=292639632&utm_source=facebook&utm_medium=remarketing&utm_campaign=incorp_DAP_20180131&gclid=CjwKCAiAyrXiBRAjEiwATI95mQZB0ynb1xF3zqsoj4wmuyH9qRIXmKz9s4yDpyDAqoqVoPO5S8IipRoCNg0QAvD_BwE&product2017=true)