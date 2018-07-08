### 1.5 Spring IoC
스프링의 핵심을 담당하는 건 바로 빈팩토리, 또는 ApplicationContext라고 불리는 것이다. 이것은 우리가 만든 DaoFactory가 하는 일을 스프링에서 좀더 일반화한 것이라고 할 수 있다

#### 1.5.1 제어권의 이전을 통한 제어관계 역전
스프링에서는 스프링이 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트를 빈이라고 부른다. 스프링에서 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트를 빈팩토리라고 한다. 보통 빈팩토리보다는 이를 좀더 확장한 ApplicationContext를 주로 사용한다.
ApplicationContext는 별도의 정보를 참고해서 빈의 생성, 관계설정등의 제어 작업을 총괄한다. DaoFactory에서는 어떤 클래스의 오브젝트를 생성할지 어떻게 연결할것인지에 대해 설정을 해줬었다. 하지만 ApplicationContext는 직접 이런정보를 담지 않고 별도로 설정정보를 담고 있는 자바 @Configuration 설정되어져 있는 자바클래스의 정보를 가져와 이를 활용하는 범용적인 Ioc 엔진이라고 할 수 있다.

##### DaoFactory를 사용하는 ApplicationContext
DaoFactory를 스프링의 빈팩토리(ApplicationContext)가 사용할수있도록 설정 정보를 만들 것이다. 먼저 빈 팩토리를 위한 오브젝트 설정을 담당하는 클래스라고 인식할수 있도록 @Configuration 이라는 어노테이션을 추가한다. 그리고 오브젝트를 만들어주는 메소드에는 @Bean 이라는 어노테이션을 추가한다.

```java

@Configuration // 애플리케이션 컨텍스트 또는 빈 팩토리가 사용할 설정정보라는 표시
public class DaoFactory{

  @Bean // 오브젝트를 새엉을 담당하는 IoC용 메소드라는 표시
  public UserDao userDao(){
    return new UserDao(connectionMaker());
  }

  @Bean
  public ConnectionMaker connectionMaker(){
    return new DConnectionMaker();
  }
}

```
이제 아래와같이 준비된 ApplicationContext의 getBean()이라는 메소드를 이용해서 UserDao의 오브젝트를 가져올 수 있다.

```java

public class UserDaoTest{
  public static void main(String[] args) throws ClassNotFoundException,SQLException{
    ApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
    UserDao dao = context.getBean("userDao",UserDao.class);
    ...
  }

}

```
context.getBean("userDao",UserDao.class);의 userDao는 DaoFactory 메소드의 이름이다. 어떻게 메소드의 이름을 설정하느냐에따라 빈을 가지고올때 String값을 변경해주면 된다.


#### 1.5.2 ApplicationContext의 동작 방식
스프링에서 ApplicationContext를 IoC컨테이너라고 하기도하고 간단히 스프링 컨테이너라고도 한다. @Configuration이 붙은 DaoFactory는 ApplicationContext가 활용하는 IoC설정정보다. 내부적으로 애플리케이션 컨텍스가 DaoFactory의 userDao() 메소드를 호출해서 오브젝트를 가져온 것을 클라이언트가 getBean()으로 요청할 때 전달 해준다. 아래 그림은 ApplicationContext의 사용되는 방식이다.

![](https://i.imgur.com/XEurNTm.png)

##### ApplicationContext를 사용했을때 장점

```
1. 클라이언트는 구체적인 팩토리 클래스를 알 필요가 없다.
2. 애플리케이션 컨텍스트는 종합 IoC서비스를 제공해준다.
  오브젝트가 만들어지는 방식, 오브젝트에 대한 후처리, 정보의 조합 설정 방식의 다변화, 인터셉티등 오브젝트를 효과적으로 활용할 수 있는 다양한 기능을 제공한다.
3. 애플리케이션 컨텍스트는 빈을 검색하는 다양한방법을 제공한다.
  타입만으로도 빈을 검색하거나 특별한 애노테이션 설정이 되어있는 빈을 찾을 수 도 있다.
```
