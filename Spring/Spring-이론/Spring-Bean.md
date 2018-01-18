## Bean
Bean(빈)이란 Spring이 관리하는 인스턴스이다.

모든 빈은 런타임에 로드되어진다. 개발자가 직접 인스턴스를 생성하고 호출, 삭제하는 것이 아니라 항상 Spring이 제공하는 Container를 통해서 관리되는 인스턴스를 우린 Bean이라고 부른다.

Spring DI container
![](https://i.imgur.com/7y3AU2y.jpg)


![](https://i.imgur.com/B3cmrIG.png)

Spring은 기본적으로 bean들을 위와같이 싱글톤으로 관리하여 사용한다. 하지만
@lazy를 넣게되면 시작될때 빈이 생성되는게아니라 프로그램이 다켜지고 직접 빈을 참조할때 초기화된다. 하지만 prototype에서는 매번 요청할때마다 객체를 새로 생성하기떄문에 먹히지 않는다.



위에서 내보인 CarDAO 클래스를 예로 들어보겠습니다.

CarDAO 클래스에는 데이터 조회의 기능을 하는 selectXXXData 형태의 메서드들이 들어가 있었습니다.

이 CarDAO 클래스는 웹 사이트에서 자동차를 조회하는 페이지 마다 다 사용되겠지요.

이러한 CarDAO 클래스를 클라이언트에서 요청이 올 떄마다 새로 만들어서 사용한다고 가정해 보면

10번 요청하면 10개의 오브젝트가 만들어지고, 100번 요청하면 100개의 오브젝트가 만들어집니다.

만약 사이트에서 해당 페이지에 초당 100번의 요청이 있다고 가정하면?

1분만 지나도 약 6,000개, 10분이면 약 6만개의 오브젝트가 만들어지게 됩니다.

아무리 가비지 컬렉션의 성능이 좋아졌다고 해도, 이는 너무 부하가 가는 작업입니다.

그래서 스프링은 각각의 빈들을 싱글톤으로 관리하고, 여러 스레드에서 이를 공유하여 사용할 수 있게 해주는 겁니다.


## applicationContext를 이용하여 싱글톤 생성

이에 비해 스프링의 applicationContext는 아주 효율적인 싱글톤 레지스트리(싱글톤을 관리한다는 의미) 입니다.


```

public class CarDAO {
    DBConnection dbConnection;

    public void setDbConnection(DBConnection dbConnection) {
        this.dbConnection = dbConnection;
    }

    Connection conn; // 위험
    List list; // 위험

    public List selectSUVData() throws Exception{
        conn = dbConnection.getConnection();

        list = // SUV 정보 쿼리
        return list;
    }

    public List selectSedanData() throws Exception{
        conn = dbConnection.getConnection();

        list = // 세단 정보 쿼리
        return list;
    }
}

```


“POJO” 클래스는 그냥 “new 하면 스스로 생성”이 가능한 클래스의 형태를 말합니다.
스프링이 모든 빈 들을 싱글톤으로 관리하기 때문입니다.
멤버변수를 선언하게되면 thread safe하지 않다.그래서 스프링은 각각의 빈들을 싱글톤으로 관리하고, 여러 스레드에서 이를 공유하여 사용할 수 있게 해주는 겁니다




```java
@Component
@Scope("prototype")   // 생략하면 싱글톤
public class Test {
       .....
}


```













출처: http://jojoldu.tistory.com/28 [기억보단 기록을]
출처: http://jojoldu.tistory.com/28 [기억보단 기록을]

[Toward the Developer](http://joont.tistory.com/144 )
출처: http://joont.tistory.com/144 [Toward the Developer]

출처: http://joont.tistory.com/144 [Toward the Developer]
출처: http://joont.tistory.com/144 [Toward the Developer]
