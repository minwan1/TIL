# 스프링 프레임워크란

## Spring 이란
스프링이 자바에서 가장 중요하게 가치를 두는것은 바로 객체지향프로그래밍이 가능한 언어라는 점이다.  자바 엔터프라이즈 기술의 혼란속에서 잃어버렸던 객체지향 기술의 진정한 가치를 회복시키고 폭넓은 혜택을 누릴 수 있도록하는것이 스프링 철학이다

또한 기존에 POJO방식인 유연하게 확장가능하면서 어떤 프레임워크에도 이식 가능한 프로그래밍으로 돌아가자가 모토이다. POJO프로그래밍을 좀 더 쉽게 할 수 있도록 빈들을 직접 관리해줌으로 단순하고 유연하게 개발할 수 있게 해줬다.

**스프링 핵심 기술**
![](https://i.imgur.com/AuHosiO.jpg)
**POJO**
* 코드가 깔끔하고 테스트하기가 쉽다.
* 특정 프레임워크에 종속되지 않는다.
* 특정 환경경에 종속되지 않는다.

## 스프링 사용 이유

1.1 프레임워크 장점
- 정형화 되어있어서 일정수준의 품질을 기대할 수 있고, 
유지보수가 쉽다. (프레임워크 숙달자 기준) 등
- 개발자가 기본적인 디자인 패턴 (DI, AOP, 서비스 추상화 등)을 강제적으로 사용하도록 한다.
- 유연성이 좋다. 등


## Spring, Spring boot 차이

## DI 란
해당 클래스가 어떤 클래스를 사용할지 결정하는게 아닌 외부에서 필요로하는 클래스를 의존성 주입을 해주는것을 의미한다. 

이렇게되면 어떤 기능이 추상화되어있으면 추상화된 클래스를 외부에서 의존성주입을 해주면되기 때문에 변경에 유연하다. 대표적으로 내가 송금을 만들기위해서는 기존에 마크넷이라는 파트너사를 이용했는데 여기에 파트너사가 바껴서 머니튠이라는 회사와 계약을했다면 단순히 외부에 의존성주입만 변경해주면 어떠한 변경이나 사이드 이펙트없이 기능을 변경 가능하다.
## IOC 란
프로그램을 개발할때 실행되는 클래스가 어떤 클래스를 생성할지 결정하고, 실행시킬지 스스로 결정했다. 하지만 이것을 자신이 관리하는게 아닌 외부 컨테이너에 맡기는것을 제어의 역전이라고한다. 스프링에서는 이러한 빈관리를해주느것이 스프링 컨테이너이다. 스프링에서는 Application context가 빈컨테이너 역할을 하게 된다. 

외부에서 빈을 입력해주는 방식은 DI가 있다. 또 이 DI 방법에도 아래와같이 setter, 새엉자, 메소드 인젝션등이 존재한다. 물론 자기가 검색하는 방법도 있다. 이것은 DL(Dependency Look up)이라고 한다.

 

![](https://i.imgur.com/Vm6PGOJ.jpg)


## 의존성 주입

## 제어의 역전(IOC)

제어의 역전은 간단히말하면 프로그램의 객체의 생성을 자신이 관리하는게 아닌 다른 외부 대상에게 맡기는것을 말한다. 이렇게 객체의 생명주기를 관리해주는 대표적인 예가 서블릿 컨테이너와 스프링컨테이너가 있다.

일반 자바프로그램을 실행시키면 가장먼저 main() 메소드가 그 main()메소드에 정의 되어져있는 소스들 기반하에 객체들이 생성되고 실행되고 소멸되어진다.

하지만 이렇게 개발자가 직접 객체의 생명주기를 관리한느게 아닌 스프링 컨테이넌나 서블릿컨테이너가 사용자가 정의한 클래스들의 생명 주기를 관리하는것을 말한다.

## 장점

인터페이스 기반 설계가 가능.
컴퍼넌트 재사용성 증가
체계적이고 효율적인 Dependency 관리


## 의존성 주입(DI)
스프링은 기본적으로 DI를 기반으로 동작한다. DI는 의존에대한 설계 패턴이다. 

예를들어 유저의 password를 암호화하는 방식으로 예를 들어보겠습니다. 일반적으로는 패스워드를 암호화하기위해서는 다음과 같이 직접 객체를 생성해서 암호화를 할것입니다.
```java
public class Password{

    private PasswordEncoder passwordEncoder = new StandardPasswordEncoder();

    public String encode(final String password){
        return passwordEncoder.encode(password);

    }
}

```

그런데 여기서 보안을 강화하기위해서 BCryptPasswordEncoder로 변경한다고 생각해보겠습니다. 그렇게되면 아래와같이 소스를 변경해야할 것 입니다.

```java
public class Password{

    private PasswordEncoder passwordEncoder = new BCryptPasswordEncoder();

    public String encode(final String password){
        return passwordEncoder.encode(password);
    }
}

```

 SimpleDriverDataSource인데, 이는 Connection Pool을 지원하지 않는다.

 CommonDBCP BasicDatasource


### 의존 객체를 직적 생성

Setter Injection : Class 사이의 의존관계를 연결시키기 위해 Setter 메소드를 이용하는 방법.
Constructor Injection : Class 사이의 의존관계를 연결시키기 위해 생성자를 이용하는 방법.
Method Injection : Method Injection은 Setter Injection과 Constructor Injection이 가지고 있는 한계점을 극복하기 위하여 지원하고 있는 DI의 한 종류다. Singleton 인스턴스와 Non Singleton 인스턴스의 의존관계를 연결할 필요가 있을 때 사용한다.


## 의존성 검색(DL)
컨테이너에 등록되어져 있는 빈을 검색하여 빈을 찾는 방법을 말합니다. 

# 컨테이너란
컨테이너를 사용하게되면 빈들의 생명주기를 따로 관리하지 않아도된다. 

## Servlet Container
Servlet의 생성, 생성 후 초기화, 서비스 실행, 소멸에 관한 모든 권한을 가지면서 Servlet의 생명주기를 관리한다.
개발자들이 직접 Servlet을 생성하고 서비스하지는 않는다.
멀티 스레딩을 지원하여 클라이언트의 다중 요청을 알아서 처리해준다.
대표적인 Conatainer에는 Tomcat, jetty, jboss 등이 있다

## IoC Container
IoC를 구현하는 프레임워크로 객체를 관리하고, 객체의 생성을 책임지고, 의존성을 관리하는 컨테이너 이다.
즉 POJO 생성, 초기화, 서비스 소멸에 관한 모든 권한을 가지면서 POJO의 생명주기를 관리한다.
개발자들이 직접 POJO를 생성할 수도 있지만, 모든 권한을 Container에게 맡긴다.


## AOP 란

OOP만으로도 전방위 걸쳐 산재되어져있는 공통되어진 소스들의 문제를 풀어 낸것이 aop다. Spring에서는 이것을 proxy를 이용해 풀어냈다. 크게 스프링에서 사용할 수 있는 aop방법은 aspectj프레임워크와, jdk proxy, cglib 방식이 있다. 특징은 aspectj같은경우 프록시 클래스를 컴파일단계에서 하기때문에 private 메소드마저 aop를 걸 수 있다는점이고 나머지는 런타임단계에서 weaving하는방식이기때문에 private에서는 걸지 못하는 특징이 있다.

AOP는 문제를 해결하기 위한 핵심 관심 사항과 전체에 적용되는 공통 모듈(Crosscutting Concerns) 사항을 기준으로 프로그래밍 함으로써 공통 모듈을 여러 코드에 쉽게 적용할 수 있도록 도와주는 역할을 합니다. AOP를 구현하는 다양한 방법이 존재하지만, 기본적인 개념은 아래 그림과 같이 공통 관심 사항 코드를 비즈니스 로직을 구현한 코드안에 삽입하는 것입니다. **대표적인게 트랜잭션 처리가 있다.**

![](https://i.imgur.com/a0AFbrG.png)

**Spring aop 구현 방법**
1. Aspectj 프레임워크 사용
2. Java jdk proxy 이용방법
3. CGLIB를 이용한 방법

**3가지 Weaving 방식**
Advice를 Weaving하는 방식에는 세가지 방식이 존재합니다.

* 컴파일시에 Weaving하기
    * ASPECTJ
* 클래스 로딩 시에 Weaving하기
    * ASPECTJ
* 런타임시에 Weaving하기(Spring proxy, cglib)
    * java jdk proxy는 인터페이스가 반드시 있어야함.
    * cglib는 인터이스가 없는 방식(spring boot는 cglib를 씀)
        * CGLIB성능이 좋아져서 Spring boot에서부터는 cglib를씀