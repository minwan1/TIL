CORS는 Spring security 전에 처리되어져야만한다.  pre-flight request가 어떤 쿠키도 포함하지 않을것이기때문이다(예 JSESSIONID). Spring security는 어떤 쿠키도 포함되어있지않다면 인증이 안된 유저이기때문에 그것을 거절할것이다.

CORS를 다루는 첫번째 쉬운방법은 CorsFilter를 다루는것이다.


```
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
			// by default uses a Bean by the name of corsConfigurationSource
			.cors().and()
			...
	}

	@Bean
	CorsConfigurationSource corsConfigurationSource() {
		CorsConfiguration configuration = new CorsConfiguration();
		configuration.setAllowedOrigins(Arrays.asList("https://example.com"));
		configuration.setAllowedMethods(Arrays.asList("GET","POST"));
		UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
		source.registerCorsConfiguration("/**", configuration);
		return source;
	}
}
```


참고문헌
* [Spring.doc](https://docs.spring.io/spring-security/site/docs/current/reference/html/cors.html)
