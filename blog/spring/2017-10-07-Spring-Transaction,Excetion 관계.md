## Spring에서 Exception 발생시 트랜잭션 처리 주의
자바에서는 총 3개의  Exception이 존재한다. Error, Checked Exception, Unchecked Exception
![](https://i.imgur.com/Nc1vuAD.png)
### Error
첫째는 java.lang.Error 클래스의 서브 클래스들이다. 에러는 시스템에 뭔가 비정상적인 상황이 발생했을 경우에 사용된다. 주로 VM에서 발생시키는 것이고 애플리케이션 코드에서 잡으려고 하면 안된다. 대부분 컴파일시 발견될 수 있는 예외이다.

### Checked Exception
java.lang.Exception 클래스와 그 아들 클래스로 정의되는 예외들은 에러와 달리 개발자들이 만든 애플리케이션 코드의 작업 중에 예외상황이 발생했을 경우에 사용된다.
Exception클래스는 다시 체크 예외와 언체크 예외로 구분 된다. 체크 예외는 Exception클래스의 아들클래스이면서 RuntimeException 클래스를 상속하지 않는 것들이고, 언체크 예외는 RuntimeException을 상속한 클래스들을 말한다.
체크 예외가 발생할 수 있는 메서드를 사용할 경우 반드시 예외를 처리하는 코드를 함께 작성해야 한다. 사용할 메서드가 체크 예외를 던진다면 이를 catch 문으로 잡든지 아니면 다시 throws를 정의해서 메서드 밖으로 던져야 한다.

### Unchecked Exception
java.lang.RuntimeException 클래스를 상속한 예외들은 명시적인 예외처리를 강제하지 않기 때문에 언체크 예외라고 불린다. 에러와 마찬가지로 이 런타임 예외는 catch 문으로 잡거나 throws로 선언하지 않아도 된다. 물론 잡거나 throws로 선언해줘도 상관없다.
런타임 예외는 주로 프로그램의 오류가 있을 때 발생하도록 의도된 것들이다. 대표적으로 오브젝트를 할당하지 않는 레퍼런스 변수를 사용하려고 시도했을 때 발생하는 NullPointerException이나 허용되지 않는 값을 사용해서 메서드를 호출할 때 발생하는 IllegalArgumentException 등이 있다. 피할 수 있지만 개발자가 부주의해서 발생할 수 있는 경우에 발생하도록 만든 것이 런타임 예외이다. 따라서 런타임 예외는 예상하지 못했던 예외상황에서 발생하는 게 아니기 때문에 굳이 catch나 throws를 사용하지 않아도 되도록 만든 것이다.

### 결론

Spring에서 선언적 트랜잭션은 기본적으로 Unchecked Exception에서만 발생한다. 하지만 강제적으로 이 방법을 해결하기 위해서는 Runtime Exception을 상속받거나 트랜잭션 전파 방식 설정을 @Transactional(rollbackFor = Exception.class)로 설정함으로써 어떤 Exception도 롤백처리를 설정할 수 있게 하는 방법이 있다. 하지만 무턱대고 Exception을 흘리고 롤백 하는 것보다는 Exception을 잡아 runtime Exception으로 throw를 하든지 관리자나 개발자에게 Exception상황을 알릴는게 낫다고 생각한다.


참고문헌
* [Spring Document](https://docs.spring.io/spring/docs/2.5.x/reference/transaction.html#transaction-declarative)
* [머루의개발블로그](http://aoruqjfu.fun25.co.kr/index.php/post/1542)
