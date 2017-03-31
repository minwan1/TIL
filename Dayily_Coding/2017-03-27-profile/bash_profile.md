# Daily Coding 2017-03-26일 spring security 암호화


```xml
<dependency>
		<groupId>org.springframework.social</groupId>
		<artifactId>spring-social-facebook</artifactId>
		<version>1.1.1.RELEASE</version>
</dependency>
```

![](http://earlybird.kr/wp-content/uploads/2013/02/oauth2_triangle2.png)


Spring Social을 제대로 이해하기 위한 레퍼런스

Spring social부터는 Spring security와 통합을 제공하였고, Spring security의 자바 config설정 지원은 설정을 좀 더 쉽게 해주었다.
* 일반적인 유저 등록폼으로 사용자 계정 생성
* 소셜 계쩡으로 사용자 등록 가능
* 유저네임과 패스워드로 로그인 가능
* SaaS Api Provider를 통해서 로그인 기능
* 어플리케이션이 페북,트위터 지원
* 어플리케이션이 반드시 규치적인 스프링 mvc사용



```html
<select class="src_country dropdown" name="srcCurrency" id="srcCurrency"
	ng-change="switchCurrencies('{{selectedSrcCurrency}}','source')"
	data-ng-model="selectedSrcCurrency">
	<option value="KRW" data-class="flag-korea"  >KRW</option>
	<option value="USD" data-class="flag-usa" selected >USD</option>
</select>

```

## profile/bash_profile
실행명령어
```
source ~/.bash_profile
````
'.profile', '.bash_profile'이 Login할 때 로드되는(source) 파일이다.
.bash_profile - 로그인할 때 로드된다. 'bash completion'이나 'nvm'같이 로그인할 때 로드해야 하는데 Bash와 관련된 것이면 여기에 넣는다.

.bashrc - 로그인하지 않고 Bash가 실행될 때마다 로드된다.








참고
* [OKIHOUSE](http://okihouse.tistory.com/entry/Facebook-간단한-Login-인증-만들기)
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /
* [김용환 블로그(2004-2017)](http://knight76.tistory.com/1864)
* [왕초보 프로그래머 Sky](http://hskimsky.tistory.com/70)
