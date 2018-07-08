http://aoruqjfu.fun25.co.kr/index.php/post/966


## 수프링 트랜잭션

서비스에 같은 클래스내에서는 같은 트랜잭션을 가진다?

Proxy 를 통한 호출이여야지 Propagation.REQUIRES_NEW 가 적용될거에요.


같은 클래스내에서 다른 트랜잭션을 같기위해서는 interface를 사용해야한다.왜냐하면 스프링 트랜잭션은 aop기반이기떄문이다. 트랜잭션을 처리할떄 프록시를 통해처리하기떄문이다.

UserService
```java
@Transactional
public String createUser(){
  CouponSevice.addCoupon();
}
```
CouponSevice
```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public String addCoupon(){
  try{
    CouponDAO.addCoupon2()
  }catch (Exception e){

  }

}
```
CouponDAO
```java
@Transactional
public String addCoupon2(){
  try{
    throw new NullPointerException();
  }cat

}
```
위와같이 addCoupon2 에서 exception날경우에는 CoponService쪽에서 새로운 트랜잭션을 선어해서 진행되기때문에 createUser메소드에 영향을 주지 않는다.


UserService
```java
@Transactional
public String createUser(){
  String id = CouponSevice.addCoupon();

//bi
  return id;
}
```
CouponSevice
```java
@Transactional
public String addCoupon(){
  try{
    CouponDAO.addCoupon2()
  }catch (Exception e){

  }

}
```
CouponDAO
```java
@Transactional
public String addCoupon2(){
  try{
    throw new NullPointerException();
  }cat

}
```
하지만 위에와같이 진행하게되면 CoponDAO에서 익셉션이 나면 addCoupon에서 catch하니까 createUser에서 business logic을 다수행하고 마지막에 리턴할때 jpa가 수행되면서 익셉션이 발생한다. 아래와같은 에러를 뱉는다.
org.springframework.transaction.TransactionSystemException: Could not commit JPA transaction; nested exception is javax.persistence.RollbackException:





참고 문헌
