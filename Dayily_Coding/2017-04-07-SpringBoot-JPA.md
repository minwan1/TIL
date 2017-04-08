# Daily Coding 2017-04-07일 spring boot-JPA



```java
@Entity // JPA 엔터티임을 표시하기위한것이다.
@Table(name="customers") //테이블에 대응하는 이름을 지정
@Data
@NoArgsConstructor// 기본생성자를 만들기 위해서 선언, jpa명세서에 따르면 엔터티 클래스에는 인자(파라미터)를 받지 않는 기본생성자를 생성해야한다.
@AllArgsConstructor // 기본생성자 외에 전체필드를 인자로 받는 생성자 생성 (intger, string,string)
public class Customer {

	@Id //프라이머리키설정
	@GeneratedValue //프라이머리키번호값 자동으로 생성됨.
    private Integer id;

	@Column(nullable=false)
    private String firstName;
	@Column(nullable=false)
    private String lastName;
}
```


Spring데이터 JPA가 제공하는 JpaRepository를 사용하여 CustomerRepository를 다시작성.
JpaRepository는 CRUD 조작용 기본메소드가 존재한다.
  * findOne
  * saved
  * findAll
  * delete


참고
* [가장 빨리 만나는 스프링 부트](http://storefarm.naver.com/sosobook/products/575628365?NaPm=ct%3Dj13jxq7c%7Cci%3Ddf62af99640375f2283b1abee1eefb6268635987%7Ctr%3Dsls%7Csn%3D421722%7Chk%3D141e7e343542920c7d3dfa4256b85462873a4dab)
* [spring.io](https://spring.io/guides/gs/accessing-facebook/)
