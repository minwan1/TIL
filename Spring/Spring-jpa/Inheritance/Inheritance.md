## Inheritance strategies with JPA,Hibernate
상속의 도메인 모델에서 사용되고 자바 컨셉의 키이다. 자바의 상속과 RDB를 관계맺는것은 자주 문제가 된다.

JPA는 상속을 구현하것을 4가지 전략을 지원한다.

* Mapped Superclass
* Table per Class
* Single Table
* Joined

![](https://i.imgur.com/Odk7RPh.png)

위에 Publication을 보면 많은 attribute들을 공유한다. 그러기떄문에 상속을 사용해야한다.

### Mapped Superclass
* 가장 간단한 접근
* 하지만 이것의 큰결함 매프드 된 최상위 객체는 Entity가아님
* 이것은 의미한다. 다형성 쿼리를 사용할수없다는것을
* 또한 단방향을 사용해야한다. from the Publication to the Author entity
* 아니라면 Author과 각각의 publication의 아들들과 관계를 맺어야한다.
* 이경우가 아니라면 다른 상속 전략을 살펴봐야한다
* 단순히 최상위 계층의 정보만 공유하기를 원한다면 이것은 좋은 전략일것이다
*

### Table per Class
* 이것은 Mapped Superclass와 비슷한 전략이다.
* 주요차이점은 슈퍼클래스도 엔터티이다라는 점이다.

![](https://i.imgur.com/xD7LG6l.png)
* 이것들은 너에게 다형성을 사용하게해준다.
* 테이블 구조가 복잡해진다.
* 이것은 작가가 쓴 publication문들을 조회할수있다. 아래와같이
```java
List authors= em.createQuery(“SELECT a FROM Author a”, Author.class).getResultList();
for (Author a : authors) {
	for (Publication p : a.getPublications()) {
		if (p instanceof Book)
		log(p.getTitle(), “book”);
		else
		log(p.getTitle(), “blog post”);
	}
}
```

* 상속이 추가되고 데이ㅓ들이 많아지면 성능이슈가생김

### Single Table
![](https://i.imgur.com/Mg96aUR.png)

### Joined
[THOUGHTS ON JAVA](https://www.thoughts-on-java.org/complete-guide-inheritance-strategies-jpa-hibernate/)



/changeLocale
