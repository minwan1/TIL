## 엔터티와 벨류값은 구분하자.

### 밸류 컬렉션 : 별도 테이블 매핑
Order 엔터티는 한 개 이상의 OrderLine(주문되어진 책목록)을 가질 수 있다. OrderLine의 순서가 있다면 다음과 같이 List타입을 이용해서 OrderLine타입의 컬렉션을 프로퍼티로 갖게 된다.







### 벨류타입의 데이터는 왠만하면 불변객체로 생성하자.
Receiver가 불변 타입이면 생성 시점에 필요한 값을 모두 전달받으므로 값을 변경하는 set 메소드를 제공하지않는다. 이는 Receiver 클래스에 


> 하이버네이트와 같은 JPA프로바이더는 DB에서 데이터를 읽어와 매핑된 객체를 생성할 때 기본 생성자를 사용해서 객체를 생성한다.
 



##
```
Order

    @ElementCollection
    @CollectionTable(name = "order_line", joinColumns = @JoinColumn(name = "order_id"))
    private List<OrderLine> orderLines;
    
```

order_line 테이블에  joinColum 참조하는 외래키를 의미한다.

```java
@SecondaryTable(
    name = "article_content",
    pkJoinColums = @PrimaryKeyJoinColumn(name = "id")
)
```

name은저장하려는 테이블이고, pkJoinColums은 저장 대상..