## Spring v1

### 목적
* 개발
* study

## 목표
* ~~springer security 구성~~
  * CustomAuthenticationProvider(인증)
  * CustomUserDetailsService(데이터커스터마이징)
  * 암호화
* 소셜로그인 기능구현
* nosql 구현
* 하둡구성
* docker구성
* deploy

##
### 1. Spring v1 springer security 구성
테이블구성
```
CREATE TABLE `users` (
  `username` varchar(50) NOT NULL,
  `password` varchar(100) NOT NULL,
  `enabled` tinyint(1) NOT NULL,
  `age` varchar(10) DEFAULT NULL,
  PRIMARY KEY (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `authorities` (
  `username` varchar(50) NOT NULL,
  `authority` varchar(50) NOT NULL,
  UNIQUE KEY `ix_auth_username` (`username`,`authority`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
##
### 2. Spring v2 소셜로그인 기능구현
