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