```java
public interface Item {
}

@Component("itemA")
public class ItemImplA implements Item {
}

@Component("itemB")
public class ItemImplB implements Item {
}
```
@Autowired
private Item itemA; // ItemA

@Autowired
private Item itemB // ItemB

// 위에방식 안될가망성큼
//밑에가 됨


```java

@Qualifier("redBean")
class Red implements Color {
   // Class code here
}

@Qualifier("blueBean")
class Blue implements Color {
   // Class code here
}

```

빈삽입방법
```java
@Autowired
@Qualifier("redBean")
public void setColor(Color color) {
  this.color = color;
}
```
아니면 하나의 어노테이션방법은
```java
@Resource(name="redBean")
public void setColor(Color color) {
  this.color = color;
}
```




@Autowired
@Qualifier("redBean")
public void setColor(Color color) {
  this.color = color;
}


@Qualifier("blueBean")


spring qualifier 예제
```
https://stackoverflow.com/questions/19414734/understanding-spring-autowired-usage
```
