# Daily Coding 2017-04-08일 spring boot-JPA DB연결처리

## 일반적인 JDBC을 이용한 DB연결
```java
public class jdbcTest {

	public static void main(String[] args) {
		try {
			Connection con = null;
			Class.forName("com.mysql.jdbc.Driver");  
			con = DriverManager.getConnection("jdbc:mysql://localhost","root", "1234");

			java.sql.Statement st = null;
			ResultSet rs = null;
			st = con.createStatement();
			rs = st.executeQuery("SHOW DATABASES");

			if (st.execute("SHOW DATABASES")) {
				rs = st.getResultSet();
			}

			while (rs.next()) {
				String str = rs.getNString(1);
				System.out.println(str);
			}
			rs.close;
			st.close;
			con.close;
		} catch (SQLException sqex) {
			System.out.println("SQLException: " + sqex.getMessage());
		}
	}

}
```

## Datasource란
* 기존의 jdbc를 사용하여 db에 접속하기 위해서는 드라이버를 로드하고 db에 접속하여 connection 객체를 받아와야 한다. 이런식이면 db에 쿼리를 보낼때 마다 드라이버를 로드하고 커넥션을 생성하고 닫게되는데 커넥션을 생성하고 다는데 시간이 소모되기에 동시접속자가 많은 사이트의 경우 전체의 성능을 낮추는 원인이 된다.(드라이버도 한번만 로드하면 되는데 불필요하게 여러번 로드하게 된다) 이런 문제를 해결하기 위해 "커넥션 풀"을 사용하는데 이것을 지원하는게 ***javax.sql.DataSource*** 클래스이다.

* ***Connection pool*** 에는 여러 개의 Connection 객체가 존재 하는데 각각을 Application에서 직접적으로 이용하면 체계적인 관리가 힘들게 되므로 DataSource라는 개념을 도입하여 사용하고 있습니다.

## JdbcTemplate
DAO Statement 처리를 위해서 Spring에서 JDBC를 지원하는데 그것이 JdbcTemplate 클래스, JdbcTemplate 클래스는 setDataSource()가 있어서 생성자 방식으로 Datasource를 지정가능하다.

## Context.xml에서 빈등록하기
```xml
<context:property-placeholder location="classpath:database.properties"/>

<!-- 인프라빈 -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="${db.driver}"/>
    <property name="url" value="${db.url}"/>
    <property name="username" value="${db.id}"/>
    <property name="password" value="${db.pw}"/>
    </bean>

<!-- 인프라빈 -->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

 <!-- autowired를 사용해도되고, 그냥 이런식으로 주입해서 써도 무방함 -->
<bean id="memberDao" class="dao.MemberDaoImpl">
           <property name="jdbcTemplate" ref="jdbcTemplate"></property>
 </bean>
```
### MemberDaoClass 안에서 동작하는 JdbcTemplate
* datasource가 존재하지않는다면 드라이버를 로드하고 커넥션풀등을 생성을해야하는데 이러한 불필요한 행동들을 없애준다.
```java
string sql ="SELECT * FROM member";
        List<Member> list = jdbcTemplate.query(sql,new MemberRowMapper())
```
```java
class MemberRowMapper implements RowMapper<Member>{
        @Override
        public Member mapRow(ResultSet rs, int arg1) throws SQLException{
            Member member = new Member();  
            member.setId(rs.getString("id"));
            member.setPw(rs.getString("pw"));        
            return member;
        }
    }
```
#### Mapper가 필요한이유
query의 결과가 list가 아닌 resultSet이기 때문에 resultSet과 list<Member>를 mapping시키는 역할을 BeanPropertyRowMapper 가 수행한다. JDBC의 resultSet (table 행 list)로부터 java의 List로(객체의 list) 한 행씩 맵핑 이전까지 while(rs.next(){}를 사용해서 직접 해줬던 작업이다.
#### Mybatis에서는 이러한 mapper 설정없이 sqlsession이 이러한처리를 해준다.
e.g.
```java
public List<Member> selectAll(){
        SqlSession session = sqlSessionFactory.openSession();
        try
        {
            return session.selectList("dao.deptMapper.selectAll");
        }
        finally
        {
            session.close();
        }
    }
```
바로 Mapper없이 List을 반환해준다.
### spring JDBC를 쓰지않고 그냥 JDBC를 쓰는방식
```java
public List<Member> getAllMember() {
        //System.out.println("dataSource:"+dataSource);
        try {
            Connection conn = dataSource.getConnection();
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return null;
    }
public DataSource getDataSource() {
        return dataSource;
    }

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }
```

## JPA란
ORM(Object Relational Mapping)이란 RDB 테이블을 객체지향적으로 사용하기 위한 기술이다. RDB 테이블은 객체지향적 특징(상속, 다형성, 레퍼런스, 오브젝트 등)이 없고 자바와 같은 언어로 접근하기 쉽지 않다. 때문에 ORM을 사용해 오브젝트와 RDB 사이에 존재하는 개념과 접근을 객체지향적으로 다루기 위한 기술이다.

##### 왜 ORM 인가?
  보통 한국은 xBatis를 현업에서 대부분 사용하는데, 쿼리 자체에 집중하고 비즈니스 로직을 쿼리에 의존하게 되면 객체지향의 장점을 놓칠 수 있는 가능성이 크다. 예를 들어 아래와 같은 User 테이블과 Order 테이블이 있다.


##
Inner Class(내부 클래스)
- 클래스 내에 선언된 클래스이다.

- 특정 클래스 내에서만 주로 사용되는 클래스를 내부 클래스로 선언한다.

- 내부 클래스에서 외부 클래스의 멤버들을 쉽게 접근할 수 있다.

- 코드의 복잡성을 줄일 수 있다.(캡슐화)



## Spring social

Spring social은 두가지 방법이 이있다. Connect Framework/Provider Sign In 방법이 있다.
```
"/connection/facebook" post
"/signin/facebook" post
```
* Connect 방법은 타임라인에 글을 보거나 쓰는용도로 많이쓰인다고
* signin은 일반 페이스북에 인증/인가와 성공한 비슷한 효과를 낸다.

내가 구현하는 방식은 signin방식이다.
먼저 signin방식을 사용하기위해서 ProviderSignInController을 알아야한다.
ProviderSignInController는 컨트롤러로 구성되어있다. /signin/{provider} (provider는 facebook/twitter etc)

그리고 Spring social에서 지정해준 table을 사용해야한다.
```sql
create table UserConnection (userId varchar(255) not null,
	providerId varchar(255) not null,
	providerUserId varchar(255),
	rank int not null,
	displayName varchar(255),
	profileUrl varchar(512),
	imageUrl varchar(512),
	accessToken varchar(512) not null,
	secret varchar(512),
	refreshToken varchar(512),
	expireTime bigint,
	primary key (userId, providerId, providerUserId));
create unique index UserConnectionRank on UserConnection(userId, providerId, rank);
```

#### JdbcUsersConnectionRepository
Userconnection테이블과 연결되어지는 spring social로부터 사용되어지는 DAO이다.


#### ConnectionSignUp
이 interface는 유저가 처음 로그인할때 어플리케이션(자신이 만들고있는)에 회원가입을 구현할수있는 곳이다. provider에서 로그인이 성공적으로 진행하면 Userconnection테이블에 프로바이더유저정보가 추가된다. 그리고 ConnectionSignUp의 execute(Connection<?> connection) 메소드가 호출된다. 이메소드에서 provider에서 제공해주는 유저의 정보을 얻어 올수 있다. 첫번째 로그인이후에는 userconnection테이블에서 유저을 찾게되면 이메소드는 더이상 호출되지 않는다.

#### ConnectionSignUp
provider측에서 인증이 끝난후에 userconnection에 정보가 저장된후에 구현해야할 인터페이스이다. ConnectionSignUp의 signIn메소드는 어플리케이션의 로그인 로직을 구현하면된다.


#### Configuration
```java
@Configuration
public class SocialConfig extends SocialConfigurerAdapter{
	@Autowired
	private Environment environment;
	@Autowired
	private DataSource dataSource;
	@Autowired
	private UserRepository userRepository;

	@Bean
	public SignInAdapter signInAdapter() {
		return new PostSocialSignInAdapter(userRepository);
	}

	@Override
	public UsersConnectionRepository getUsersConnectionRepository(ConnectionFactoryLocator connectionFactoryLocator) {
		JdbcUsersConnectionRepository repository = new JdbcUsersConnectionRepository(dataSource,
				connectionFactoryLocator, Encryptors.text(environment.getProperty("social.security.encryptPassword"),
						environment.getProperty("social.security.encryptSalt")));
		repository.setConnectionSignUp(new SocialImplicitSignUp(userRepository));
		return repository;
	}
}
```

#### 만드는순서
ExampleUserDetail
RepositoryUserDetailService
UserRepository
User
SimpleSocialUserDetail Service

#### 인증과정에서 세가지 컴포넌트가 필요하다.
1. 인증된 유저 세부사항을 담을 클래스
2. UserDetailSerice 인터페이스
3. SocialUserDetailService

1번은 내용 SocialUser라는 인터페이스로 이것을 확장해서 사용하면된다.



http://javacents.com/adding-social-sign-in-to-a-spring-boot-app/
참고
* [가장 빨리 만나는 스프링 부트](http://storefarm.naver.com/sosobook/products/575628365?NaPm=ct%3Dj13jxq7c%7Cci%3Ddf62af99640375f2283b1abee1eefb6268635987%7Ctr%3Dsls%7Csn%3D421722%7Chk%3D141e7e343542920c7d3dfa4256b85462873a4dab)
* [내일의 나를 만드는 방법](http://choong0121.tistory.com/entry/DataSource-란)
* [keep moving keep living](http://chocobeans.tistory.com/entry/spring-JDBC-사용하기 )
* [초보개발자 이야기.](http://ra2kstar.tistory.com/134)
* [갱짱.study](http://gangzzang.tistory.com/entry/Java-내부클래스inner-class)
* [JavaCents](http://javacents.com/adding-social-sign-in-to-a-spring-boot-app/)
