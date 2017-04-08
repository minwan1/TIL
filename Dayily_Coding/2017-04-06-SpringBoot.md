# Daily Coding 2017-04-06일 spring boot
```java
@Autowired
DataSource dataSource;
```
위와같이 정의 하면 @EnableAutoConfiguration의 자동등록 기능은 무시된다.


```java
DataSourceBuilder factory = DataSourceBuilder.create(this.dataSourceProperties.getClassLoader()).url(this.dataSourceProperties.getUrl()).username(this.dataSourceProperties.getUsername()).password(this.dataSourceProperties.getPassword());
```
인스턴스 생성



참고
* [가장 빨리 만나는 스프링 부트](http://storefarm.naver.com/sosobook/products/575628365?NaPm=ct%3Dj13jxq7c%7Cci%3Ddf62af99640375f2283b1abee1eefb6268635987%7Ctr%3Dsls%7Csn%3D421722%7Chk%3D141e7e343542920c7d3dfa4256b85462873a4dab)
* [spring.io](https://spring.io/guides/gs/accessing-facebook/)
