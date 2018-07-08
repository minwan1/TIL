## security, oauth


servlet filter 와aop기반으로 보안을 가진다

spring seucrity라이브러리를 추가하고 기본적으로 security를 설정하지 않으면 리소스에 보호되어진 리소스에 접근하기위해서는 베이직 로그인 인증이필요하다.


@EnableResourceServer 를 설정하게되면 Oauth2 토큰을 인증할 수 있게 spring security 필터가 활성화가 된다. 이설정을 하게되면 oauth endpoints ex) /oauth/authorize. 와같은 URL을 제외하고는 모두 보호된다.


5.9 Multiple HttpSecurity
We can configure multiple HttpSecurity instances just as we can have multiple <http> blocks. The key is to extend the WebSecurityConfigurationAdapter multiple times. For example, the following is an example of having a different configuration for URL’s that start with /api/.

```java
@EnableWebSecurity
public class MultiHttpSecurityConfig {
	@Bean                                                             1
	public UserDetailsService userDetailsService() throws Exception {
		// ensure the passwords are encoded properly
		UserBuilder users = User.withDefaultPasswordEncoder();
		InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
		manager.createUser(users.username("user").password("password").roles("USER").build());
		manager.createUser(users.username("admin").password("password").roles("USER","ADMIN").build());
		return manager;
	}

	@Configuration
	@Order(1)                                                        2
	public static class ApiWebSecurityConfigurationAdapter extends WebSecurityConfigurerAdapter {
		protected void configure(HttpSecurity http) throws Exception {
			http
				.antMatcher("/api/** ")                               3
				.authorizeRequests()
					.anyRequest().hasRole("ADMIN")
					.and()
				.httpBasic();
		}
	}

	@Configuration                                                   4
	public static class FormLoginWebSecurityConfigurerAdapter extends WebSecurityConfigurerAdapter {

		@Override
		protected void configure(HttpSecurity http) throws Exception {
			http
				.authorizeRequests()
					.anyRequest().authenticated()
					.and()
				.formLogin();
		}
	}
}
```
1
Configure Authentication as normal

2
Create an instance of WebSecurityConfigurerAdapter that contains @Order to specify which WebSecurityConfigurerAdapter should be considered first.

3
The http.antMatcher states that this HttpSecurity will only be applicable to URLs that start with /api/

4
Create another instance of WebSecurityConfigurerAdapter. If the URL does not start with /api/ this configuration will be used. This configuration is considered after ApiWebSecurityConfigurationAdapter since it has an @Order value after 1 (no @Order defaults to last).




https://docs.spring.io/spring-security/site/docs/current/reference/html/jc.html#multiple-httpsecurity
