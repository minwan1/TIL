## LinkedHashMap
기존 HashMap과다르게 LinkedHashMap은 입력시의 순서에 맞게 값을 가져올수 있습니다.



```java
LinkedHashMap<Character,Character> linkmap = new LinkedHashMap<Character,Character>(){
    @Override
    protected boolean removeEldestEntry(Entry<Character,Character> eldest){
        return size() == 6 ? true : false;
    }
}

```


```java
// interator 다음요소 return
String deleteKey = map.keySet().iterator().next(); // 가장 처음에 들어갔던 키를 리턴해줌.
map.remove(deleteKey);
```