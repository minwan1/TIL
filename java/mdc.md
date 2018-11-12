Log4j MDC
로그 관련 컨텍스트 값을 Thread Local로 설정하고 유지하여 로그로 남길 수 있게 해 준다.

```java
public void doFilter(ServletRequest request, ServletResponse response,
    FilterChain chain) throws IOException, ServletException {
 
    // MDC 값설정
 
    try{
        chain.doFilter(request, response); 
    } finally {
        MDC.clear();
    }	
}	
```



