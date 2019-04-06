# Spring-AOP, Proxy 란?

## AOP란 ?<br>

AOP는 문제를 해결하기 위한 핵심 관심 사항과 전체에 적용되는 공통 모듈 사항을 기준으로 프로그래밍 함으로써 공통 모듈을 여러 코드에 쉽게 적용할 수 있도록 도와주는 역할을 합니다. AOP를 구현하는 다양한 방법이 존재하지만, 기본적인 개념은 아래 그림과 같이 공통 관심 사항 코드를 비즈니스 로직을 구현한 코드안에 삽입하는 것입니다.
![](https://i.imgur.com/a0AFbrG.png)
위에 그림에서 공통기능은 직접적으로 호출되지 않습니다. 핵심로직을 구현한 코드를 컴파일하거나, 컴파일된 클래스를 로딩하거나, 또는 로딩한 클래스의 객체를 생성할 때, Proxy객체를 통해 호출할 때 AOP가 적용됩니다.

## AOP용어

```
-Joinpoint
Advice를 적용 가능한 지점을 의미합니다. 메소드호출, 필드값 변경 등이 Joinpoint에 해당 합니다.
-Pointcut
Joinpoint의 부분집합으로서 실제로 Advice가 적용되는 Joinpoint를 나타냅니다. 스프링에서는 정규 표현식이나 AspectJ의 문법을 이용하여 Poincut을 재정의 할 수 있습니다.
-Advice
언제 공통 관심 기능을 핵심로직에 적용할 지를 정의하고 있습니다. 예를들어, '메서드를 호출 하기전에 트랜잭션 시작' 기능을 적용한다는 것을 정의하고 있는 것입니다.
-Weaving
Advice를 핵심로직코드에 적용하는것을 weaving이라고 한다라고 합니다. 즉 공통코드를 핵심로직코드에 삽입하는것을 weaving이라고 합니다.
-Aspect
여러 객체에 공통으로 적용되는 기능을 Aspect라고 합니다. 트랜잭션이나, 보안등이 Aspect의 좋은 예입니다.
```

## 3가지 Weaving 방식
Advice를 Weaving하는 방식에는 세가지 방식이 존재합니다.

1. 컴파일시에 Weaving하기
2. 클래스 로딩 시에 Weaving하기
3. 런타임시에 Weaving하기

일반적으로 컴파일시와 클래스 로딩 시에는 weaving하는 방식은 AspectJ라이브러리를 추가하여 구현할때 사용됩니다. 런타임시에 weaving하는 방식은 Spring-AOP에서 사용하는 방식인데 소스코드나 클래스 정보 자체를 변경하지 않습니다. 대신 프록시를 생성하여 AOP를 적용합니다. 프록시 기반의 AOP는 핵심 로직을 구현할 객체에 직접 접근하는것이 아니라 아래 그림과 같이 중간에 프록시를 생성하여 프록시를 통해 핵심 로직의 객체에 접근하는 것입니다.
![](https://i.imgur.com/bFfetFM.jpg)
이때 프록시는 핵심 로직을 실행하기 전 또는 후에 공통 모듈 기능을 적용하는 방식으로 AOP를 구현하게 됩니다.

## Spring-AOP란?
스프링은 자체적으로 프록시 기반의 AOP를 지원하고 있습니다. 따라서 스프링 AOP는 메서드 호출 Joinpoint만을 지원합니다. 필드 값 변경과 같은 Joinpoint를 사용하고 싶다면 AspectJ와 같이 다양한 Joinpoint를 지원하는 AOP프레임워크를 사용해야 합니다.

스프링은 크게 3가지 방식으로 AOP를 제공합니다.

1. XML 스키마 기반의 POJO클래스를 이용한 AOP구현
2. AspectJ에서 정의한 @Aspect 애노테이션 기반의 AOP구현
3. 스프링 API를 이용한 AOP구현

어떤 방식을 사용하더라도 내부적으로 프록시를 이용하여 AOP가 구현되기 때문에 프록시를 통한 메소드호출만 AOP를 적용할 수 있다는 것을 유의 해야 합니다. 예를 들어 내부클래스내에서 다른 메소드를 호출한다고 하면 그것은 프록시를 거쳐 가는것이 아니기 때문에 AOP가 적용되지 않습니다.

## 프록시를 이용한 AOP 구현
스프링은 프록시를 이용하여 AOP를 구현합니다. 스프링은 Aspect의 적용대상이 되는 객체에 대한 프록시를 만들어 제공합니다. 비지니스 로직에 접근할 때 대상 객체로 바로 접근하는 게 아닌 프록시를 통해서 간접적으로 접근하게 됩니다. 이 과정에서 프록시는 공통 기능을 실행한 뒤 대상 객체의 실제 메서드를 호출하거나 또는 대상객체의 실제 메소드를 호출한 후에 공통기능을 실행합니다.
![](https://i.imgur.com/63YMS1o.png)
대상 타겟은 결국 빈 객체가 생성 되는데, 런타임시에 오브젝트 생성 설정에 따라서 스프링 컨테이너가 지정한 빈 객체 대한 프록시 객체를 생성하고, 원본 빈 객체 대신에 프록시 객체를 사용하게 합니다.

프록시 객체를 생성하는 방식은 대상 객체가 인터페이스를 구현하고 있느냐 없느냐 여부에 따라 달라집니다. 2가지 방식이 존재하는데 JDK Dynamic Proxy, CGLIB를 이용하여 프록시를 생성하는 방식이 있습니다.

### JDK Dynamic Proxy
스프링은 자바 리플렉션 API가 제공하는 java.lang.reflect.Proxy를 이용하여 프록시 객체를 생성합니다. 아래 그림과 NConnectionMaker,DConnectionMaker와 같이 동일한 인터페이스를 구현하게되면 UserDao와 같은 클라이언트는 인터페이스를 통해서 필요한 메서드를 호출하게 됩니다. 하지만, ***인터페이스를 기반으로 프록시 객체를 생성하기 때문에 인터페이스에 정의되어 있지 않은 메서드에 대해서는 AOP가 적용되지 않는 점에 유의해야합니다.***
![](https://i.imgur.com/gX6THnw.png)

### CGLIB
대상객체가 인터페이스를 구현하고 있지 않고 바로 클래스를 사용한다면, 스프링은 CGLIB를 이용하여 클래스에 대한 프록시 객체를 생성합니다. CGLIB는 대상 클래스를 상속 받아 프록시를 구현합니다. 따라서 클래스가 final인경우에는 프록시를 생성할 수 없습니다.

#### 강제로 CGLIB를 이용하여 Proxy를 생성하는 방법

@EnableAspectJAutoProxy 어노테이션 속성으로 proxyTaretClass란 것이 있습니다. 위에서 말한대로 Spirng AOP는 Proxy class를 생성함으로 AOP를 구현 합니다. AOP의 대상이 되는 클래스가 Interface를 구현하고 있을 경우 Spring은 Interface를 이용하는 JDK Dynamic Proxy를 만들어서 AOP를 구현하고 Interface를 구현하지 않고 클래스에 대해서는 CG-LIB를 이용한 대상클래스의 상속을받아 서브클래스를 만들어 이를 Proxy삼아 AOP 구현하게 됩니다. 이 속성 값을 주지 않으면 기본값은 false이기 때문에 대상이 되는 클래스가 interface를 이용한것은 자동으로 JDK Dynamic Proxy를 이용해서 AOP를 생성하고 아닌경우에는 CG-LIB를 이용해 클래스  Proxy로 AOP를 구현하게 됩니다. 이 속성을 true로 주게되면 인터페이스 또한 CG-LIB를 이용하여 서브클래스 생성하는 방식으로 AOP의 프록시를 구현하게 됩니다.


출처 <br>
* [최범균](http://book.naver.com/bookdb/book_detail.nhn?bid=7918153) <br>
* [Spring document](https://docs.spring.io/spring/docs/4.2.x/spring-framework-reference/html/transaction.html)<br>
