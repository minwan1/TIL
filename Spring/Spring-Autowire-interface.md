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
