# 개요
DataSource, jdbcTemplate을 알아보고 이것을 이용한 불필요한 생성등을 없애고 데이터베이스 연결하기
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
위와같은 불필요한 생성들을 없애주는게 DataSource,jdbcTemplate이다.
## Datasource란?
* 기존의 jdbc를 사용하여 db에 접속하기 위해서는 드라이버를 로드하고 db에 접속하여 connection 객체를 받아와야 한다. 이런식이면 db에 쿼리를 보낼때 마다 드라이버를 로드하고 커넥션을 생성하고 닫게되는데 커넥션을 생성하고 다는데 시간이 소모되기에 동시접속자가 많은 사이트의 경우 전체의 성능을 낮추는 원인이 된다.(드라이버도 한번만 로드하면 되는데 불필요하게 여러번 로드하게 된다) 이런 문제를 해결하기 위해 "커넥션 풀"을 사용하는데 이것을 지원하는게 ***javax.sql.DataSource*** 클래스이다.

* ***Connection pool*** 에는 여러 개의 Connection 객체가 존재 하는데 각각을 Application에서 직접적으로 이용하면 체계적인 관리가 힘들게 되므로 DataSource라는 개념을 도입하여 사용하고 있습니다.

## JdbcTemplate란?
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
### spring JDBC를 쓰지않고 dataSource을 이용한 JDBC를 쓰는방식
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




참고
* [내일의 나를 만드는 방법](http://choong0121.tistory.com/entry/DataSource-란)
* [keep moving keep living](http://chocobeans.tistory.com/entry/spring-JDBC-사용하기 )
* [초보개발자 이야기.](http://ra2kstar.tistory.com/134)
