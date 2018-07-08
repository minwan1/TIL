## Spring-data-JPA-querydsl
각종 데이터베이스나 검색엔진이 모두 자기만의 데이터 검색을 위한 쿼리 문법을 가지고 있다. 이렇게 다양한 각 데이터 쿼리 솔류션들의 쿼리 문법을 모두 익히는 것은 쉽지 않다. queryDsl은 몇가지 데이터관리 엔진에 대해서 하나의 통일된 문법으로 쿼리를 작성할 수 있게 해준다. 그리고 여기에 더해서 중요한 특징은 쿼리작성을 java 코드로 하며 type safe 하게 작성할 수 있다는 점이다.

~batis를 사용하는 개발자에게는 자바로 쿼리를 작성한다는것이 익숙하지 안음을 넘어서 불쾌하게 느껴지기 까지 할 수 있겠지만 빠른 개발과 쉬운 디버깅을 통해 개발자가 일찍 퇴근할 수 있는것보다  중요한 미덕은 없을것이다.

![](http://i.imgur.com/ftaKwmx.png)

### Type safe(형식 안전성)
자바로 쿼리를 작성하는 criteria도 존재한다. 하지만 queryDsl은 Type safe라는 개념이 존재한다. 기존 솔루션은 쿼리로 문자열을 만든후 orm엔진에 넘기는 방식인데 여기에는 큰문제가 있다. 컴파일 하는과정에서 에러를 발견 할 수 없다는것이다. 또한 만약 스키마가 변경된다면 프로그램 안에서 그부분관 관련된 쿼리 문자열이 모두 수정되어야한다. JPA의 criretia도 필드이름을 stiring 으로 넘기는 방식이라 type safe한 방식은 아니다.

com.mysema.querydsl와 com.querydsl 차이는  3. * 버전이 mysema버전이고 4. * 버전부터 com.querydsl이다. 처음에는 querydsl은 mysema의 소유였는데 지금은 querydsl팀의 소유이다.
### 플러그인 추가및 dependency 추가
```xml
<dependency>
	<groupId>com.mysema.querydsl</groupId>
	<artifactId>querydsl-jpa</artifactId>
	<version>3.6.2</version>
</dependency>
	<dependency>
		<groupId>com.mysema.querydsl</groupId>
		<artifactId>querydsl-apt</artifactId>
		<version>3.7.2</version>
		<scope>provided</scope>
	</dependency>

	<plugin>
	<groupId>com.mysema.maven</groupId>
	<artifactId>apt-maven-plugin</artifactId>
	<version>1.1.3</version>
	<executions>
		<execution>
			<goals>
				<goal>process</goal>
			</goals>
			<configuration>
				<outputDirectory>target/generated-sources/java</outputDirectory>
				<processor>com.mysema.query.apt.jpa.JPAAnnotationProcessor</processor>
			</configuration>
		</execution>
	</executions>
</plugin>

or(밑에사용해야함)

<dependency>
	<groupId>com.querydsl</groupId>
	<artifactId>querydsl-jpa</artifactId>
</dependency>
<dependency>
	<groupId>com.querydsl</groupId>
	<artifactId>querydsl-apt</artifactId>
	<scope>provided</scope>
</dependency>


<plugin>
	<groupId>com.mysema.maven</groupId>
	<artifactId>apt-maven-plugin</artifactId>
	<version>1.1.3</version>
	<executions>
		<execution>
			<goals>
				<goal>process</goal>
			</goals>
			<configuration>
				<outputDirectory>target/generated-sources/java</outputDirectory>
				<processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
			</configuration>
		</execution>
	</executions>
</plugin>

```
mvn compile 명령어를 통해서 q객체들을 생성해줘야한다. target/generated-sources/java 위치에 q객체들이 생성 됬을것이다.


엔터티 매니저와 사용하는 방법
```java
pulbic void queryDSL(){
	EntityManager em = emf.createEntityManager();
	JPAQuery query = new JPAQuery(em);
	QMember qMember = new QMember("m");

	List<Member> members = query.from(qMember).where(qMember.name.eq("회원1"))
	.orderBy(qMember.name.desc()).list(qMember);

}

```





사용방법
QueryDslPredicateExecutor 를 통해 쉽게 select 해올 수 있다

```java
public interface QueryDslPredicateExecutor<T> {
    T findOne(Predicate predicate);
    Iterable<T> findAll(Predicate predicate);
    Iterable<T> findAll(Predicate predicate, OrderSpecifier<?>... orders);
    Page<T> findAll(Predicate predicate, Pageable pageable);
    long count(Predicate predicate);
}


출처: http://netframework.tistory.com/entry/13-queryDSL-Spring-Data-JPA
```

```
@Repositorypublic interface BookDao extends JpaRepository<Book, Integer>, QueryDslPredicateExecutor<Book> {

}


```

QueryDslRepositorySupport 를 통해 update,delete 처리를 한다.
```java
@Repository
public abstract class QueryDslRepositorySupport {

    private final PathBuilder<?> builder;

    private EntityManager entityManager;
    private Querydsl querydsl;

    public QueryDslRepositorySupport(Class<?> domainClass) {
        Assert.notNull(domainClass);
        this.builder = new PathBuilderFactory().create(domainClass);
    }

    @PersistenceContext
    public void setEntityManager(EntityManager entityManager) {

        Assert.notNull(entityManager);
        this.querydsl = new Querydsl(entityManager, builder);
        this.entityManager = entityManager;
    }

    @PostConstruct
    public void validate() {
        Assert.notNull(entityManager, "EntityManager must not be null!");
        Assert.notNull(querydsl, "Querydsl must not be null!");
    }

    protected EntityManager getEntityManager() {
        return entityManager;
    }

    protected JPQLQuery from(EntityPath<?>... paths) {
        return querydsl.createQuery(paths);
    }

    protected DeleteClause<JPADeleteClause> delete(EntityPath<?> path) {
        return new JPADeleteClause(entityManager, path);
    }

    protected UpdateClause<JPAUpdateClause> update(EntityPath<?> path) {
        return new JPAUpdateClause(entityManager, path);
    }

    @SuppressWarnings("unchecked")
    protected <T> PathBuilder<T> getBuilder() {
        return (PathBuilder<T>) builder;
    }

    protected Querydsl getQuerydsl() {
        return this.querydsl;
    }
}

```

Prodicate() 계층이 있어야할듯 무조건,


QueryDslPredicateExecutor 의 경우는 select 를 해올떄 사용되고
QueryDslRepositorySupport 의 경우는 update,insert의 경우 사용된다.






출처: http://netframework.tistory.com/entry/13-queryDSL-Spring-Data-JPA [Programming is Fun]



참고
* [Programming is Fun](http://netframework.tistory.com/entry/13-queryDSL-Spring-Data-JPA )
* [시나몬 브레드](http://adrenal.tistory.com/23)
* [스택오버플러우](https://stackoverflow.com/questions/32469814/the-difference-between-com-mysema-query-and-com-querydsl)
