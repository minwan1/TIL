## JPA Cache
네트워크를 통해 데이터베이스 접근하는 시간 비용은 애플리케이션 서버에서 내부 메모리에 접근하는 시간 비용보다 수만에서 수십만배 이상 비싸다. 따라서 조회한 데이터를 메모리에 캐시해서 데이터 베이스 접근 횟수를 줄이면 애플리케이션 성능을 획기적으로 개선할 수 있다. 영속성 컨텍스트 내부에 있는 엔터티를 보관하는 저장소가 있는데 이것을 1차 캐시라고한다. 일반적인 웹 애플르케이션 환경은 트랜잭션을 시작하고 종료할 때 까지만 1차캐시가 유효하다.(따라서 애플르케이션 전체로보면 데이터베이스 접근 횟수를 획기적으로 줄이지는 못한다.)

#### First Cache
![](http://i.imgur.com/sXfsPbz.png)

* 1차캐쉬는 영속성컨텍스트에 캐쉬한내용이없으면 디비에서 정보를 조회해 리턴해준다.
* 1차캐시는 같은 엔티티가 있으면 해당 엔티티를 그대로 반환한다. 따라서 1차캐시는 객체 동ㅇ일성(a==b)를 보장한다.

하이버네이트를 포함한 대부분의 JPA구현체들은 애플리케이션 범위의 캐시를 지원하는데 이것을 공유 캐시 또는 2차 캐시라 한다. 이런 2차 캐시를 활용하면 애플리케이션 조회 성능을 획기적으로 향상할 수 있다.

#### Second Cache
![](http://i.imgur.com/H9emJYD.jpg)

2차캐시는 동시성을 극대화하려고 캐시한 객체를 반환하지 않고 복사본을 만들어서 반환한다. 만약 캐시한 객체를 그대로 반환하면 여러곳에서 같은 객체를 수정하는 문제가 발생할 수 있다. 이문제를 해결하려면 객체에 락을 걸어야 하는데 이렇게 하면 동시성이 떨어질 수 있다.

2차캐시 모드설정
```java
@Cacheable
@Entity
public class Member{
  @Id
  @GeneratedValue
  private Long id;
}

```
다음은 shard-cache-mode를 설정해서 애플리케이션 전체에 캐쉬를 어떻게 적용할지 옵션을 설정하는것이다.
```xml
<persistence-unit name="test">
  <shared-cache-mode>ENABLE_SELECTIVE</shared-cache-mode>
<persistence-unit>
```
```
<bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
  <property name="sharedCacheMode" value="ENABLE_SELECTIVE" />
  ...
```

SharedCacheMode 모드 설정
ALL : 모든 엔티티를 캐시한다.
NONE : 캐시를 사용하지 않는다.
ENABLE_SELECTIVE : Cacheable(true)로 설정된 엔티티만 캐시를 적용한다.
DISBLE_SELECTIVE : 모든엔티티를 캐시하는데 (Cacheable(false)로 명시된 엔터티는 캐시하지 않는다.)
UNSPECIFIED : JPA를 구현체가 정의한 설정을 따른다.
