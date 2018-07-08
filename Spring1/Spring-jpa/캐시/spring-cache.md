## Spring cache, ehcache 구현

### Cache
일반적으로 애플리케이션에서 데이터베이스를 접근하게 되면 많은 시간과 비용을 소비하게 된다. 그래서 반복적으로 일어나면서 많은 양의 데이터를 가지고 와야 하는 작업은 부담이 된다. 그래서 많은 사람들은 이러한 문제를 해결하기 위해서 캐시를 사용한다. 캐시를 사용하면 데이터베이스 접근하는 횟수를 획기적으로 개선할 수 있기 때문이다.

### Spring Cache
그래서 Spring에서는 이러한 Cache 기능을 지원하기 위해서 Spring caching abstraction을 지원한다. Spring caching abstraction는 다른 캐시 솔루션을 Spring CacheManager를 통해서 쉽게 사용할 수 있도록 해준다. Spring CacheManager는 다양한 캐시의 솔루션들을 코드에 최소의 영향으로 다양한 캐싱 솔루션(라이브러리)을 도입할 수 있게 해준다. Spring caching abstraction은 자바 메소드에 캐싱을 적용한다. 그래서 메소드가 실행될 떄마다 메소드의 넘어온 파라미터에 따라 캐쉬를 적용하게 된다. 예를들어 2초이상걸리는 A라는 메소드를 호출 했을 때 파라미터로 1의 값을 넘겼고, 결과값 으로 2를 받았다고 하면 그다음에 다시 한번 누군가가 A메소드를 1의 파라미터를 넘겼으면 A메소드는 2초의 시간이 걸리지 않고 캐시되어 있는 데이터 2를 바로 리턴해준다. 이것의 범위는 애플리케이션내의 범위이다.

### Spring Cache 다양한 캐시 솔루션 도입방법
Spring 3.1 부터 간단하게 캐시를 도입할 수 있게 되었다. Spring에서 제공해주는 CacheManager인터페이스만 구현체에 따라 구현 해주면 된다. 다른 캐시 라이브러리들을 추가해서 구현체에 사용할 수 있다.Spring Boot에서 캐시를 사용하기위해서는 spring-boot-starter-cache를 추가하는데 기본적으로 spring-boot-starter-cache 이외에 아무 서드파트 라이브러리를 추가 하지 않는다면 기본설정인 SimpleCacheConfiguration 가 동작하며 CacheManager로는 ConcurrentMapCacheManager가 빈으로 등록이 된다. ConcurrentMapCacheManager는 가벼워서 많이들 사용하지만 Product용으로는 Ehcache 사용하는 것 같다. Ehcache를 사용하면 좀 더 확장적이고 분산시스템등 커스터마이징을 할 수 있기 때문이다.

### Spring Cache 사용방법
먼저 Spring Cache를 사용하기 위해서는 메이븐에 아래와같이 라이브러리를 추가해줘야한다.
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```
위와 같이 메이븐에 내용을 추가하고 @EnableCaching 를 선언하게 되면 Spring container에 빈이 등록된다. 기본적으로는 위에서 말햇듯이 ConcurrentMapCacheManager이 등록된다. 하지만 상황에 맞게 다른 캐쉬 구현체를 등록할 수 있다. 그 다음 Cache를 적용하기를 원하는 메소드에 @Cacheable 를 붙이면 된다. 이어노테이션 이외에도 몇개의 어노테이션이 존재한다. 그리고 그 어노테이션에 맞는 몇개의 속성들이 존재한다. 아래는 그 어노테이션과 속성에대한 간단한 설명이 있다.

다음은 스프링 캐시 설정 어노테이션들이다.
| 어노테이션     | 설명                             |
| -------------- | -------------------------------- |
| @Cacheable     | 메소드에 캐시 트리거 설정        |
| @CachePut      | 메소드 실행과 방해없이 캐시 갱신 |
| @CacheEvict    | 캐시되있는 데이터 지우기         |
| @CacheConfig   | 캐쉬 관려설정                    |
| @EnableCaching | 스프링 캐시활성화                |

다음은 캐시 어노테이션들의 속성이다
| 어노테이션     | 설명                                                    |
| -------------- | ------------------------------------------------------- |
| value          | 캐시의 이름                                             |
| key            | 캐시할 키를 설정(기본설정하지 않으면 파라미터로 설정됨) |
| condition      | 특정 조건에 따라 캐시를 할지 않을지 결정                |
| cacheManager등 | 해당 캐시 매니저 설정가능                               |

좀더 많은 설정들을 확인하기 위해서는 [공식문서](https://docs.spring.io/spring/docs/3.2.x/spring-framework-reference/html/cache.html)를 참조하면 된다.

#### Spring Cache ConcurrentMapCacheManager 적용
먼저 간단하게 ConcurrentMapCacheManager를 이용해서 캐시를 구현해보자.
```java
@SpringBootApplication
@EnableCaching
public class CacheApplication {

	public static void main(String[] args) {
		SpringApplication.run(CacheApplication.class, args);
	}
}
```
일단 위와 같이 @EnableCaching를 선언함으로 Spring Cache를 사용하겠다고 선언한다. 그리고 사용하고자하는 메소드위에 @Cacheable 어노테이션을 적는다. 이렇게 되면 캐시기능이 적용된다.
```java
@Cacheable(value = "members")
public Member findById(long id){
        System.out.println("test");

        if(id == 1)
            return  Member.builder()
                    .id(1)
                    .age(15)
                    .name("김철수")
                    .build();
        else
            return  Member.builder()
                    .id(2)
                    .age(17)
                    .name("김아무개")
                    .build();
    }
```
이 findById 메소드에 멤버 id로 1이라는 id를 호출한 후에 다시 한번 똑같은 id로 호출하게 되면 이 findById메소드를 타지않고 캐시 되어 있는 데이터를 가지고오는것을 확인할 수 있다. 여기에서 제대로 캐시기능이 적용 되었는지 테스트하기위해서 System.out.println("test")로 로거를 찍었는데 2번이상 1번아이디의 유저를 호출하려고 해도 아래와 같이 한번만 test가 로그에 찍히는 것을 확인할 수 있다.
![](https://i.imgur.com/KB4IssF.png)

위에 ConcurrentMapCacheManager캐시 관련 소스는 여기 [깃허브](https://github.com/minwan1/blog-example/tree/concurrent-cache-example/cache)에서 확인할 수 있다.


#### Spring Cache Ehcache 적용
이번에는 Ehcache를 적용하는 예제를 구현할 것이다. Ehcache를 사용하기 위해서는 아래와 같이 메이븐을 추가해줘야 한다.

```xml
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>2.10.2.2.21</version>
</dependency>
```
그리고 Ehcache를 사용하기 위해서는 아래와 같이 xml 설정을 해줘야 한다. Ehcache xml 설정에 대한 세부 내용은 [공식 홈페이지](http://www.ehcache.org/)에서 확인할 수 있다.
```xml
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="ehcache.xsd"
    updateCheck="true"
    monitoring="autodetect"
    dynamicConfig="true">

    <diskStore path="java.io.tmpdir" />

    <cache name="members"
        maxEntriesLocalHeap="100"
        maxEntriesLocalDisk="1000"
        eternal="false"
        timeToIdleSeconds="300"
        timeToLiveSeconds="600"
        memoryStoreEvictionPolicy="LFU"
        transactionalMode="off">
        <persistence strategy="localTempSwap" />
    </cache>

</ehcache>
```
간단히 위의 내용을 설명하자면 'members'라는 캐시의 이름을 선언했고, 최대 100개의 member들을 heap store에 담을 수 있고, 최대 1천개의 members은 diskStore에(java.io.tmpdir) 보관될 것이다. 이 member는 5분 이상 변화가 없거나 10분 이상 살아있다면 이캐시는 만료 될 것이다.

Ehcache XML 설정을 한후 Spring Cache를 등록하면 기본적으로 ConcurrentMapCacheManager가 등록되니까 Ehcache를 사용하기위해서 아래와같이 ehCacheCacheManager 빈을 등록해야한다.
```java
@Bean
public CacheManager cacheManager() {
    return new EhCacheCacheManager(ehCacheCacheManager().getObject());
}

@Bean
public EhCacheManagerFactoryBean ehCacheCacheManager() {
    EhCacheManagerFactoryBean factory = new EhCacheManagerFactoryBean();
    factory.setConfigLocation(new ClassPathResource("ehcache.xml"));
    factory.setShared(true);
    return factory;
}
```
위에 ConcurrentMapCacheManager에서는 member를 조회하는 곳에서만 캐시를 사용했었는데 여기에서는 멤버를 조회하고, 저장된 캐시를 제거하는 예제를 만들 것이다. 먼저 멤버 조회하는 Service는 위의 예제와 동일하게 가지 갈 것이다. 그리고 아래와 같이 member 지웠다고 가정하는 메서드를 만들 것이다.

```java
@CacheEvict(value = "members",key ="#id")
    public void deleteMember(long id){
        System.out.println("deleteMember");
    }
```
CacheEvict 이어노테이션은 캐시에 저장된 데이터를 지우는 역할을 한다. 예를 들어 아까 1이라는 id의 멤버를 조회했을 경우 id 1로 조회하는 데이터는 캐시가 된것을 볼 수 있었다. 하지만 이메소드를 탄후에 다시 조회하려고 하면 캐시 된 내용이 사라지고 findById 메소드가 실행되는 모습을 볼 수 있다.
![](https://i.imgur.com/PCwOLfU.png)
위에 사진은 Member id가 1인유저를 findById 메소드를 이용해 조회했다가,다시 Member id가 1인 유저를 deleteMember 메소드를 호출한후 다시 Member id가 1인유저를 findById 메소드를 이용해 조회한 결과이다. 2번이상 findById를 탔음에도 불구하고 test라는 로그가 2번 찍힌 것을 볼 수 있다. 그 이유는 deleteMember를 탐으로써 캐시된 내용이 삭제되었기 때문이다.

Ehcache에대한 소스는 여기 [깃허브](https://github.com/minwan1/blog-example/tree/ehcache-example/cache)에서 확인할 수 있다.

### 마무리하며
캐시에 대해 많은 내용들이 있지만 먼저 간단하게 캐시를 알아봤다. 캐시를 적용한다면 디비에 접근하는 시간, 비용들을 많이 아낄 수 있을 거라고 생각한다. 그렇다고해서 모든 애플리케이션 내에 캐시를 적용 한다고 해서 좋은 것은 아닐것 같다. 어느 정도 기준을 두고 캐시를 사용해야 한다고 생각한다. 안 그러면 캐시에 의해 과부하가 걸릴 수 있고 관리가 쉽지 않아 보이기 때문이다. 그래서 게시판이나 블로그 등 웹 기반의 애플리케이션은 최근에 사용된 데이터가 또다시 사용되는 경향을 있는 곳에서 사용하면 좋다고 한다. 최범균님 블로그를 보면 80:20 법칙에 따라 20%의 데이터가 전체 조회 건수의 80%를 차지할 경우 캐시를 사용함으로써 성능을 대폭적으로 향상시킬 수 있을 것이라고 말씀하셨다. 이 정도의 기준인 데서 캐시를 쓰면 정말 효율적으로 성능의 폭을 대폭 향상시킬 수 있고 시간, 자원 등을 아낄 수 있을 거라고 생각한다.


참고
* [MKyong](https://www.mkyong.com/spring/spring-caching-and-ehcache-example/) [docs.spring.io](https://docs.spring.io/spring/docs/current/spring-framework-reference/integration.html#cache)
* [머루의 개발블로그](http://wonwoo.ml/index.php/post/1551)
* [자바 ORM 표준 JPA 프로그래밍](http://book.naver.com/bookdb/book_detail.nhn?bid=9252528)
* [최범균님 블로그](http://javacan.tistory.com/entry/133)
