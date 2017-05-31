## Spring-data-JPA

### Spring-data
spring-data는 최근 나와있는 다양한 데이터관리솔루션(mysql,mongdb,radis)을 동일한 방식의 인터페이스로 사용할수 있게해주는 프로젝트이다.

### Spring-data-JPA
자바 ORM 표준 JPA는 SQL 작성없이 객체를 데이터베이스 직접 저장할 수 있게 도와준다. 그리고 이러한 JPA의 구현체로 하이버네이트,EclipseLink, openJPA가 존재한다. 이러한 spring-data의 인터페이스로써 JPA를 다룰수 있게해주는게 spring-data-jpa 이다.
![](http://i.imgur.com/Dr6tjoO.png)
### JPA 장점
* CRUD SQL을 작성할 필요가 없음.
* 조회된 결과를 객체로 매핑하는것도 자동화가 되어있음.
* jpa에서 제공하는 네이티브 sql로 실제 sql로도 사용가능함.
* 데이터베이스 조회성능 이슈의 문제는 jpa문제라기보다는 sql문제임
* 생산성이 좋아지고 유지보수할게 없어짐
* 데이터베이스 종류변환에서도 flexible하게 이동가능


## 자바 -> 데이터베이스 데이터 CRUD
기존에는 CRUD 를 작성하려면 너무많은 sql과 jdbc api를 코드로 작성해야한다. 왜냐하면 데이터베이스는 객체 구조와는 다른 데이터 중심의 구조를 가지므로 객체를 데이터베이스 직접 저장하거나 조회할수는 없다. 이러한문제를 해결하기위해 나온것이 JPA이다.

### Spring-data-JPA 원리
Spring-data-JPA는 Reposytory인터페이스만 작성하면 나머지는 Spring-data-JPA에서 상속받은 Reposytory을 검색하여 런타임시기에 그메소드에 맞게 구현한 레파지토리에 객체를 생성해서 스프링 빈으로 등록한다.


##
Reposytory<Employee,Long> 2번쨰는 식별(프라이머리키 타입)을넣는것, 여기에서는 id가 long형이기때문에 long을넣음

##리파지터리 인터페이스 메서드 작성 규칙
Spring-data-JPA모듈은 리파지터리로 사용할 인터페이스는 o.s.data.reposytory.Reposytory 인터페이스를 상속받아야 한다.

###
```

```








참고
* [시나몬 브레드](http://adrenal.tistory.com/23)




http://projects.spring.io/spring-hateoas/
JSON형식을 만들어줌
