## Spring Exception


자바에서 크게 에러는 2가지가 존재한다.
* 문법에러
	* 오타와같이 자바 구문에서 에러가나는 경우 이다. 이것은 컴파일시 에러로 해결이가능하다.
* 실행에러
	* 실행 에러는 프로그램 실행시 상황에 따라 발생하는 오류이다.
	* 실행 에러안에서 크게 또 논리에러,시스템에러,예외사항으로 나뉘어진다.
	*
### 자바 Exception

여기서 문법적인 에러는 컴파일시 대부분 수정이 가능하다. 실행에러는 컴파일은 수행되었으나 실행시에 문제가 되는 경우로논리적인 에러, 시스템에러, 예외 사항의 발생이다. 이중 논리적인 에러는 프로그래머가 코드를 잘못 작성해서 생기는 경우이며, 시스템적인 에러는 프로그래머의 의지와는 상관없이 발생하는 것이므로 프로그래머가 처리할 수 있는 방법은 없다. 예외사항은 정상적으로 프로그램이 동작중에 발생할 수 있는 여러가지 사항들이며 자바는 언어차원에서 이런 예외적인 사항의 처리를 지원하기 때문에 탄탄한 프로그램을 작성할 수 있다.


아래는 exception class들의 계층 구도이다.
![](http://i.imgur.com/NHoaID9.jpg)
이미지에서 체크되어져있는 부분의클래스(thorws 하니까)들은 예외처리를 필수로 안해줘도되지만 언체크된부분들은 필수로 예외처리를 해야만 컴파일이 되어진다.

### Spring Exception

Srping에서 추천해주는 해결법은 3.2버전과 4.x버전이 존재한다. 3.2번전은 HandlerExceptionResolver, @ExceptionHandler을 사용하는것이고, 3.2이후 버전은 @controllerAdvice를 사용하는 방법이 있다.

#### 해결방법 1컨트롤 레벨 @ExceptionHandler
@ExceptionHandler 핸들러는 컨트롤러에서 잘작동한다. 예를들어 아래와 같이 "/cal/divide"를 호출했을대 연산중에 에러가 날경우 아래 ArithmeticException을 타게된다.
```java
public class FooController{

	@RequestMapping("/cal/divide")
	public String divide(int op1/int op2){
		model.addAttribute("result",op1/op2);
		return "cal/result"
	}

	@ExceptionHandler(ArithmeticException.Class)
	public String handleException(){
		return "error/exception";
	}
}
```
 하지만 이것은 단점이 하나 존재한다. @ExceptionHandler는 오직 컨트롤러에서만 사용 가능하다. 이것을 basecontroller로 만들어 상속하여 이러한 문제를 조금은 해결할 수 있지만. 하지만 basecontroller 상속하게되면 다른 클래스들를 상속하여 사용할 수 없게 된다.

####  해결방법 2 HandlerExceptionResolver
HandlerExceptionResolver은 모든 던져지는 exception을 처리 할 수 있다. 이것은 또한 우리에게 restapi에서 처리할 수 있는 ExceptionHandler을 구현할 수 있게 해준다.
* ExceptionHandlerExceptionResolver
	* 이것은 spring 3.1에 도입되어있고 DispatcherServlet에 기본으로 활성화 되어 있다.
	* 이것은 실제로  @ExceptionHandler의 핵심 코어 구성요소이다.
* DefaultHandlerExceptionResolver
	* 이것은 spring 3.0에 도입되어있고 DispatcherServlet에 기본으로 활성화 되어 있다.
	* 이것은 http client error 4xx server error 5xx 해당하는 http status 코드를 처리하는데 일반적인 스프링 익셉션으로 사용한다.
	* 적절하게 응답코드에 status 코드를 설정가능하지만 바디에는 어떠한 설정도 불가능하다. 또한 rest api에게 status code를 제공하기에는 충분한 정보를 제시하지 못한다.
	* 등등

####  해결방법 3 @ControllerAdvice
@ControllerAdvice는 글로벌로 @ExceptionHandler를 지원한다. 아래는 server 내부에러나 resource를 찾지 못했을때 응답해줄 수 있는 핸들러이다. 아래와같이 특정 컨트롤러에 @ControllerAdvice를 추가하여 에러가 발생시 throw 처리를 통해 특정 handler로 응답처리를 손쉽게 해줄 수 있다.
```java
@ControllerAdvice
public class ControllerStatusAdvice {

    @ExceptionHandler(value = { HttpServerException.class })
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    @ResponseBody
    protected ErrorMessage handleConflict(RuntimeException ex, WebRequest request) {
    	ErrorMessage er = new ErrorMessage();
    	er.setStatus(HttpStatus.INTERNAL_SERVER_ERROR.value());
    	er.setMessage(ex.getMessage());
        return er;
    }

    @ExceptionHandler(value = {HttpNotFoundException.class })
    @ResponseStatus(HttpStatus.NOT_FOUND)
    @ResponseBody
    protected ErrorMessage handleNotfound(RuntimeException ex, WebRequest request) {
    	ErrorMessage er = new ErrorMessage();
    	er.setStatus(HttpStatus.NOT_FOUND.value());
    	er.setMessage(ex.getMessage());
        return er;
    }

}

@Controller
public class TestController {

	@RequestMapping(value="/test")
	public void ExecuteException(){
		throw new HttpServerException("TEST");
	}

}

```


##
```java
throw new HttpBadRequestException("zxcv");
```
이렇게 던지면 그클래스안에서 try catch로 를 쓰면 따로 throw로 또 던져줘야한다.
```java
try{
}catch (HttpBadRequestException e){
	thorw HttpBadRequestException 던져줘야할듯
}catch (Exception e){
	Exception 처리
}

```
***아래와같이 던지면 어차피 HttpBadRequestException을 아래 catch에서 잡혀서 serverexception처리가 된다.***
```java
try{
	throw HttpBadRequestException("test");
catch (Exception e){
	HttpServerException("erorr");
	Exception 처리
}
```



참고
* [Baeldung](http://www.baeldung.com/exception-handling-for-rest-with-spring)
* [개발이하고싶어요](http://hyeonstorage.tistory.com/199)
