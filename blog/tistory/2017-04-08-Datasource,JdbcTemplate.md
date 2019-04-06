## JDBC, Connection, ConnectionPool, Datasource, JdbcTemplate이란 ?
먼저 위 타이틀을 알아보기전에 아래소스를 보자. 왜 위 API들이 우리에게 필요한지 알아보자. 우리가 자바를 사용해서 디비에서 List형태의 데이터를 불러오려면 아래와같이 복잡한 소스를 작성해야한다. 하지만 자바와 스프링에서 제공해주는 API DataSource, JdbcTemplate등을 이용하면 훨씬 더 깔끔하고 안정적이 구조로 데이터를 조회할 수 있다. 먼저 DataSource와 JdbcTemplate을 알아보기전에 자바에서 어떻게 데이터베이스에 접근하는지 알아볼것이다.

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

### JDBC란
JDBC는 Java Database Connectivity의 약어로 자바 언어로 다양한 종류의 관계형 데이터베이스에 접속하고 SQL문을 수행하여 처리하고자할때 사용되는 표준 SQL 인터페이스 API이다. 데이터베이스마다 연결방식과 통식 규격이 따로 있다. 프로그램을 데이터베이스에서 제공하는 방법으로 연결한다면 해당 데이터베이스와 관련된 기술적 내용을 자세히 배워야한다. 그리고 데이터베이스를 변경한다면 많은 변경을 해줘야한다.하지만 이론적으로는 각 DBMS에 맞는 JDBC를 받아주게되면 쉽게 DBMS를 변경할 수 있게 된다. 아래그림은 JDBC를 이용해 데이터베이스를 접근하는 순서를 표현하는 그림이다.

![](https://i.imgur.com/dbpdf3P.png)

### Connection Pool이란?
Connection Pool 이란 클라이언트의 요청 시점에 Connection을 연결하는 것이 아니라 미리 일정수의 Connection 을 만들어 놓고 필요한 어플리케이션에 전달하여 이용하도록 하는 방법이다. Container 구동 시 일정수의 Connection 객체를 생성하게 되며 클라이언트의 요청에 의해 Application 이 DBMS 작업을 수행해야 하면 Connection Pool 에서 Connection 객체를 받아와 작업을 진행하며 작업이 끝나면 다시 Connection Pool 에 반납하는 과정을 거친다.

### Connection이란?
DriverManager.getConnection()은 실제 자바프로그램과 데이터베이스를 네트워크상에서 연결을 해주는 메소드이다. 연결에 성공하면 DB와 연결 상태를 Connection 객체로 표현하여 반환한다. Connection은 아래의 그림과 같이 네트워크상의 연결 자체를 의미한다. 자바프로그램과 DB사이의 길로 볼 수 있다. 보통 Connection하나당 트랜잭션 하나를 관리한다. 트랜잭션은 하나 이상의 쿼리에서 동일한 Connection 객체를 공유하는 것을 뜻한다. Mybatis의 SqlSession, Hibernate에 TransactionManager등의 Close가 이루어지면 Connection을 ConnectionPool에 반납하게 된다.
![](https://i.imgur.com/OWPyJNm.png)

### Statement
Connection으로 길을 만들었으면 DB쪽으로 SQL문을 보내야고 그결과값을 받아야하는데 이역할을 하는것이 Statement객체이다.

### Datasource란?
***javax.sql.DataSource*** 는 인터페이스는 ConnectionPool을 관리하는 목적으로 사용되는 객체로 Application에서는 이 Datasource 인터페이스를 통해서 Connection을 얻어오고 반납하는 등의 작업을 구현해야하는 인터페이스이다. Connection Pool에는 여러개의 Connection 객체가 생성되어 운용되어진다. 각각을 직접 웹 어플리케이션에서 이용하면 체계적인 관리가 힘들게 되므로 DataSource라는 개념을 도입하여 사용하고 있다. DataSource 인터페이스는 Connection pool을 어플리케이션단에서 어떻게 관리할지를 구현해야하는 인터페이스이다.


### JdbcTemplate란?
Statement 처리를 위해서 Spring에서 JDBC를 지원하는데 그것이 JdbcTemplate 클래스, JdbcTemplate 클래스는 setDataSource()가 있어서 생성자 방식으로 Datasource를 지정가능하다. 아래는 Mybatis를이용한 간단한 JdbcTemplate예제이다.
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
#### MemberDaoClass 안에서 동작하는 JdbcTemplate
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





참고
* [내일의 나를 만드는 방법](http://choong0121.tistory.com/entry/DataSource-란)
* [keep moving keep living](http://chocobeans.tistory.com/entry/spring-JDBC-사용하기 )
* [초보개발자 이야기.](http://ra2kstar.tistory.com/134)
* [처음 해보는 Servlet & JSP 웹 프로그래밍](http://www.yes24.com/24/goods/37235901)
