# @EqualsAndHashCode

- equals(), hashCode()를 생성해준다.

- static or transient 가 아닌 모든 필드들이 대상이 된다.

- 명시적으로 특정 필드를 제외하게 하려면 exclude={'field', 'field2'}

- 명시적으로 특정 필드를 포함하게 하려면 of={'field', 'field2'}

- 기본적으로 getter가 있으면 해당 메소드가 호출된다. 이를 사용하지 않고 필드를 직접 호출하려면 doNotUseGetter=true


##


callSuper 속성을 통해 equals와 hashCode 메소드 자동 생성 시 부모 클래스의 필드까지 감안할지 안 할지에 대해서 설정할 수 있습니다.

즉, callSuper = true로 설정하면 부모 클래스 필드 값들도 동일한지 체크하며, callSuper = false로 설정(기본값)하면 자신 클래스의 필드 값들만 고려합니다.


```java
User user1 = new User();
user1.setId(1L);
user1.setUsername("user");
user1.setPassword("pass");

User user2 = new User();
user1.setId(2L); // 부모 클래스의 필드가 다름
user2.setUsername("user");
user2.setPassword("pass");

user1.equals(user2);
// callSuper = true 이면 false, callSuper = false 이면 true
```

##

무분별한 @EqualsAndHashCode 사용 자제
@EqualsAndHashCode는 상당히 고품질의 equals, hashCode 메소드를 만들어준다. 잘 사용하면 좋지만 남발하면 심각한 문제가 생긴다.

특히 문제가 되는 점은 바로 Mutable(변경가능한) 객체에 아무런 파라미터 없이 그냥 사용하는 @EqualsAndHashCode 애노테이션이다.

```java
@EqualsAndHashCode
public static class Order {
    private Long orderId;
    private long orderPrice;
    private long cancelPrice;
 
    public Order(Long orderId, long orderPrice, long cancelPrice) {
        this.orderId = orderId;
        this.orderPrice = orderPrice;
        this.cancelPrice = cancelPrice;
    }
}
 
Order order = new Order(1000L, 19800L, 0L);
 
Set<Order> orders = new HashSet<>();
orders.add(order); // Set에 객체 추가
 
System.out.println("변경전 : " + orders.contains(order)); // true
 
order.setCancelPrice(5000L); // cancelPrice 값 변경
System.out.println("변경후 : " + orders.contains(order)); // false
```

## 참고

[권남](http://kwonnam.pe.kr/wiki/java/lombok/pitfall)