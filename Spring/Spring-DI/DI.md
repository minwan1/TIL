## DI

의존성 주입방식
### 어노테이션

#### setter방식
```java
public class Car {
	private CarManager carManager;

	@Resource(name="carManager")
	public void setPrinter(CarManager carManager) {
		this.carManager = carManager;
  }
```

#### 생성자

```java
public class Car {
	private CarManager carManager;

  @Autowired
	public Car(@Qualifier("hyundai") CarManager carManager) {
		this.carManager = carManager;
  }
```
#### seter

```java

```java
public class Car {
  @Inject
  @Named("hyundai")
	private CarManager carManager;
```

```

##

di순위
생성자, 셋터, 멤버  필드

##

1. Component scan이 진행된뒤에 Annotation-config가 진행됨
