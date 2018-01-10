## @JsonIgnore

이건 Recursion문제를 해결하기위해서 나온 어노테이션은 아니다. 단지 그냥 serialized,deserialized과정에서 null을 할당해버린다.



## @JsonManagedReference와 @JsonBackReference
@JsonManagedReference와 @JsonBackReference는 본질적으로 순환참조를 방어하기 위한 Annotation이다.


## @JsonIdentityInfo이란
jackson 2.0이하는 @JsonManagedReference와 @JsonBackReference방식을 이용하지만 2.0이후 부터는 recursion이슈를 JsonIdentityInfo어노테이션을 이용해서 해결한다.

## Srping jpa에서 fetch되지않은 오브젝트들은  jackson serializaion과정을 피하는 방법

Spring 3.1.2이후부터는 MappingJackson2HttpMessageConverter에 hibernate-module을 추가해서 사용하면 lazy loading시에는 jackson으로 변경하지 않는다. 참고로 Recursion 이슈는 해결해주지 않는다.
// json serialized할때 lazy loading은 json으로 변경하지않음
```xml
<dependency>
  <groupId>com.fasterxml.jackson.datatype</groupId>
  <artifactId>jackson-datatype-hibernate4</artifactId>
</dependency>
```





## @JsonProperty
getter/setter 의 이름을 property 와 다른 이름을 사용할 수 있도록 설정한다. Database 를 자바 클래스로 매핑하는데 DB의 컬럼명이 알기 어려울 경우등에 유용하게 사용할 수 있다.

다음과 같은 테이블이 있을 경우
```
CREATE TABLE Users (
  u INT NOT NULL,
  a INT NOT NULL,
  e VARCHAR(80) NOT NULL
);
```
다음과 같이 JsonProperty 를 사용하면 DB 의 컬럼명을 변경하지 않아도 가독성을 높일 수 있다.
```
public class User
{
    @JsonProperty("userId");
    public Integer u;

    @JsonProperty("age");
    public Integer a;

    @JsonProperty("email");
    public String e;
}
```
json 으로 변환된 결과
```
{
    "userId": 1,
    "age": 13,
    "email": "user@host.com"
}
```
참고
* [Binary Cube](http://binarycube.tistory.com/1)
