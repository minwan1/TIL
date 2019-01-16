## Collection

### @ElementCollection and @CollectionTable

너의 모바일유저들은 두개이상의 주소, 폰넘버를 가질 수 있다. 너는 이요구 조건을 만족하기 위해서는 별도의 테이블을 만들어야한다.


![](https://i.imgur.com/n9elEsR.jpg)

테이블 둘다 user_phone_numbers and user_addresses 는 user의 id를 참조한다. 이것은 흔히 one to many로 구현할 수 있다. 

하지만 JPA는 좋은 솔루션을 가진다. 

```java
@Embeddable
public class Address {
    @NotNull
    @Size(max = 100)
    private String addressLine1;

    @NotNull
    @Size(max = 100)
    private String addressLine2;

    @NotNull
    @Size(max = 100)
    private String city;

    @NotNull
    @Size(max = 100)
    private String state;

    @NotNull
    @Size(max = 100)
    private String country;

    @NotNull
    @Size(max = 100)
    private String zipCode;

    public Address() {

    }

    public Address(String addressLine1, String addressLine2, String city, 
                   String state, String country, String zipCode) {
        this.addressLine1 = addressLine1;
        this.addressLine2 = addressLine2;
        this.city = city;
        this.state = state;
        this.country = country;
        this.zipCode = zipCode;
    }

    // Getters and Setters (Omitted for brevity)
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotNull
    @Size(max = 100)
    private String name;

    @NotNull
    @Email
    @Size(max = 100)
    @Column(unique = true)
    private String email;

    @ElementCollection
    @CollectionTable(name = "user_phone_numbers", joinColumns = @JoinColumn(name = "user_id"))
    @Column(name = "phone_number")
    private Set<String> phoneNumbers = new HashSet<>();

    @ElementCollection(fetch = FetchType.LAZY)
    @CollectionTable(name = "user_addresses", joinColumns = @JoinColumn(name = "user_id"))
    @AttributeOverrides({
            @AttributeOverride(name = "addressLine1", column = @Column(name = "house_number")),
            @AttributeOverride(name = "addressLine2", column = @Column(name = "street"))
    })
    private Set<Address> addresses = new HashSet<>();


    public User() {

    }

    public User(String name, String email, Set<String> phoneNumbers, Set<Address> addresses) {
        this.name = name;
        this.email = email;
        this.phoneNumbers = phoneNumbers;
        this.addresses = addresses;
    }

    // Getters and Setters (Omitted for brevity)
}
```

@ElementCollection 이것을 사용할것이다. 모든 기록은 테이블에분리 되어 기록되어진다. 

CollectionTable은 모든 컬렉션기록을 저장하는 테이블의 네임을 구체화한다.


https://www.callicoder.com/hibernate-spring-boot-jpa-element-collection-demo/


@ElementCollection을 통해 값을 추가할 경우, JPA 구현체는 해당 컬렉션의 모든 데이터를 삭제하고 다시 모든 데이터를 추가한다. PK가 존재하지 않기 때문에 발생하는 현상이다.
이 현상을 조금이나마 줄이려면 @OrderColumn을 사용하면 된다.



### 값 추가시 전체 삭제후 재추가하는 문제
@ElementCollection을 통해 값을 추가할 경우 아래와 같이 JPA 구현체는 해당 컬렉션의 모든 데이터를 삭제하고, 다시 모든 데이터를 insert문을 실행한다. PK가 존재하지 않기 때문에 발생하는 현상이다.

```sql
Hibernate: 
    select
        order0_.order_number as order_nu1_4_0_,
        order0_.create_at as create_a2_4_0_,
        order0_.update_at as update_a3_4_0_ 
    from
        purchase_order order0_ 
    where
        order0_.order_number=?
Hibernate: 
    select
        orderlines0_.order_number as order_nu1_3_0_,
        orderlines0_.price as price2_3_0_ 
    from
        order_line orderlines0_ 
    where
        orderlines0_.order_number=?
Hibernate: 
    delete 
    from
        order_line 
    where
        order_number=?
Hibernate: 
    insert 
    into
        order_line
        (order_number, price) 
    values
        (?, ?)
Hibernate: 
    insert 
    into
        order_line
        (order_number, price) 
    values
        (?, ?)
Hibernate: 
    insert 
    into
        order_line
        (order_number, price) 
    values
        (?, ?)

```

이 현상을 조금이나마 줄이려면 @OrderColumn을 사용하면 된다. @OrderColumn 사용하게되면 다삭제하지 않고 기존 데이터문을 update를 실행하고 마지막 추가한 데이터만 삽입하게 된다.

만약 벨류타입을 많이사용한다면 @ElementCollection보다 onetomany를 사용하는것이 좋다.


@OrderColumn을 사용하면 자동으로 index를 추가한다. 그럼 아래와같이 해당 주문에 맞춰서 index을 값지게 된다.

order_number | 가격 | line_idx
------- | ------- | -------
order | 3000 | 0
order1 | 3000 | 0
order | 30 | 1
order2 | 3000 | 0
order | 30 | 2