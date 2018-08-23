# ObjectMapper
ObjectMapper는 public getter or stter를 기반으로 serialize해서 json형태의 string을 만든다.


```java
ObjectMapper mapper = new ObjectMapper();
//        mapper.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS, false); // 객체가 널이거나 public함수가없는 객체는 제외하고 직렬화를 한다.
        mapper.setVisibility(PropertyAccessor.FIELD, JsonAutoDetect.Visibility.ANY); // 멤버변수를 기반으로 직렬화를 한다.
```