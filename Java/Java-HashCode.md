자바에서 String으로 equals()메소드는 많이사용하는것같다. 그런데 정작 객체값을 비교하는적은 많이 없는것같다. 이러한 이유가 내가 equals 메소드를 잘몰라서 그런게 아닐까하고 equals 및 hashCode에대해 좀 정리를 해봤다.

## 객체 비교(equals())

다음은 Object의 equals() 메소드이다.
```java
public boolean equals(Object obj){...}
```

Object 클래스의 equals()는 아래와같은 기능으로써 이 메소드는 비교연사자인 == 과 동일한 결과를 리턴한다. 오로지 참조값이 같은지, 다시말하면 동일 객체인지를 확인하는 기능이다.
```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

자바에서는 두 객체를 동등 비교할 때 equals() 메소드를 흔히 사용한다. equals() 메소드는 두 객체를 비교해서 논리적으로 동등하면 true를 리턴하고 그렇지 않으면 false를 리턴한다. 논리적으로 동등하다는것은 둘의 참조값이 다르더라도 객체 내부 value는 같다는것을 의미한다.
대표적인예가 String class이다. String class는 equals()메소드를 재정의해서 번지비교가 아닌 문자열리 비교한다. 아래는 해당 내용의 소스이다.
```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

위 소스를 보면 Object로 객체를 받아 하나하나 문자열을 비교하는것을 볼 수 있다. char은 참조값이 아닌 기본값이기 때문에 == 만으로도 값비교가 된다.
예를들어 Member라는 클래스가 있는데 여기에 Id값이 같은면 동등객체로 취급하고싶다면 equals()메소드를 재정의해서 id 필드값이 같음을 비교하면 된다. 아래는 그의 해당하는 예제이다.
```java
public class Member {
  public String id;
  public Member(String id){
    this.id = id;
  }

  @Override
  public boolean equals(Object obj){
    if(obj instanceof Member){
      Member member = (Member) obj;
      if(id.equals(member.id)){
        return true;
      }
    }
  }
  return false;
}
```
위와같이 equals메소드를 재정정의 하게되면 Member 객체를 통해 객체를 비교할때 Id가 같다면 두객체는 동등 객체가 될것이다.

* 동일성 비교는 == 비교다. 객체 인스턴스의 주소 값을 비교한다.
* 동등성 비교는 equals() 메소드를 사용해서 객체 내부의 값을 비교한다.

## Java hash code란
객체 해시코드란 객체를 실별할 하나의 정수값을 말한다. Object의 hashCode() 메소드는 객체의 메모리 번지를 이용해서 해시코드를 만들어 리턴하기 때문에 객체 마다 다른 값을 가지고 있다. 객체의 값을 동등성 비교시 hashCode()를 오버라이딩할 필요성이 있는데, 컬렉션 프레임워크에서 HashSet, HashMap, HashTable은 다음과 같은 방법으로 두 객체가 동등한지 비교한다.
우선 hashCode() 메소드를 실행해서 리턴된 해시코드 값이 같은지를 본다. 해시 코드값이 다르면 다른 객체로 판단하고, 해시코드값이 같으면 equals()메소드로 다시 비교한다. 이두개가 모두 맞아야 동등 객체로 판단한다.
![](https://i.imgur.com/dShPCEh.png)

다음 예제를 보면 Key클래스 equals 메소드를 재정의해서 number필드값이 같으면 true를 리턴하도록했다. 그러나 hashCode메소드는 재정의 하지 않았기 때문에 Object의 hashCode() 메소드가 사용된다.

```java
public class Key{
  public int number;

  public Key(int number){
    this.number = number;
  }

  @override
  public boolean equals(Object obj){
    if(obj instanceof Key){
      Key compareKey = (Key) obj;
      if(this.number == compareKey.number){
        return true;
      }
    }
    return false;
  }
}
```

이런경우 두개의 동등한 객체를 HashMap의 식별키로 Key 객체를 사용하면 저장된 값을 찾아 오지 못한다. 왜냐하면 number 필드값이 같더라도 hashCode() 메소드에서 리턴하는 해시코드가 다르기 때문에 다른 식별키로 익식하기 때문이다. 실제 예제를 실행해보고 확인해보자.

```java
public class KeyExample{
  public static void main(String[] args){

    //Key 객체를 식별키로 사용해서 String 값을 저장하는 HashMap 객체생성.
    HashMap<Key, String> hashMap = new HashMap<Key, String>();

    //식별키 "new key(1)"로 "홍길동"을 저장함
    hashMap.put(new Key(1), "홍길동");

    //식별키 "new key(1)"로 "홍길동"을 읽어옴
    String value = hashMap.get(new Key(1));
    System.out.println(value);
  }
}
```
위에 내용에서 Hash Code를 정의 하지 않았기 때문에 값은 null이 조회 될것이다. 만약 의도한대로 홍길동을 읽으려면 아래와같이 hashCode를 재정의 해야한다.

```java
public class Key{
...
  @override
  public int hashCode(Object obj){
    return number;
  }

}
```
저장할 때의 new Key(1)과 읽을때의 new Key(1)은 다른 참조값의 다른 객체지이지만 HashMap은 hashCode()의 리턴값이 같고, equals() 리턴값이 true가 나오기 때문에 두 객체는 동등 객체로 평가하여 하나의 키처럼키 사용하게 된다. 즉, 같은 식별키로 인식한다는 뜻이다. **이러한 이유로 객체의 동등 비교를 위해서는 Object의 equals() 메소드만 재정의하지 말고 hashCode()메소드도 재정의해서 논리적 동등 객체일경우 동일한 해시코드가 리턴되도록 해야한다.**

여기에서 다시한번말하면 키를 기반으로찾는것이지 객체의 참조값으로 찾는것은 아니다. 고로 hash코드값이 같다고해서 객체의 참조값이 같은것은 아니다.

### 해시코드 생성(hash(), hashCode())
Object.hash(Object... values) 메소드는 매개값으로 주어진 값들을 이용해서 해시 코드를 생성하는 역할을하는데, 주어진 매개값들로 배열을 생성하고 Arrays.hashCode(Object[])를 호출해서 해시코드를 얻고 이값을 리턴한다. Object.hash(Object... values) 메소드는 hashCode()를 재정의할 때 리턴값을 생성하기 위해 사용하면 좋다. 클래스 하나가 여러가지 필드를 가지고 있을 때 이 필드들로부터 해시코드를 생성하게 되면 동일한 필드값을 가지는 객체는 동일한 해시코드를 가질 수 있다.

```java
@Override
public int hashCode() {
  return Objects.hash(field1, field2, filed3);
}
```
