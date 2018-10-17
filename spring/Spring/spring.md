# 스프링 프레임워크란

## 제어의 역전(IOC)

제어의 역전은 간단히말하면 프로그램의 객체의 생성을 자신이 관리하는게 아닌 다른 외부 대상에게 맡기는것을 말한다. 이렇게 객체의 생명주기를 관리해주는 대표적인 예가 서블릿 컨테이너와 스프링컨테이너가 있다.

일반 자바프로그램을 실행시키면 가장먼저 main() 메소드가 그 main()메소드에 정의 되어져있는 소스들 기반하에 객체들을 생성하고 실행되고 소멸되어진다. 즉 객체의 생명주기를 main()메소드



기존에 자바 기반으로 어플리케이션을 개발할 때 자바 객체를 생성하고 서로간의 의존 관계를 연결시키는 작업에 대한 제어권은 보통 개발되는 어플리케이션에 있었다.

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


### 의존 객체를 직정 생성하는 방식의 단점
* 

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
