# Spring Events

다음 가이드라인을 따라야한다
* event 는 ApplicationEvent를 따라야만한다.
* publisher는 ApplicationEventPublisher를 빈등록해야만한다.
* Listener는 ApplicationListner인터페이스를 구현해야만한다.


2. A Custom Event

스프링은 기본적으로 동괴화된 커스텀 이벤드를 만드는 기능이 있다. 이것은 몇가지 이점을가지는데 publisher는 transaction context에 참여할 수 있다.



# Better application events in Spring Framework 4.2


# Annotation 기반 이벤트 Listener
스프링 4.2버전전에는 커스텀 Event Listner를 등록하기 위해서는 아래와같이 등록해줘야했다.

```java 
@Component
public class CustomSpringEventListener implements ApplicationListener<CustomSpringEvent> {
    @Override
    public void onApplicationEvent(CustomSpringEvent event) {
        System.out.println("Received spring custom event - " + event.getMessage());
    }
}

```

스프링 4.2부터는 @EventListener 또는 @TransactionalEventListener 어노테이션을 이용하면 아래와같이 메소드의 시그니처를 매치하여 자동으로 ApplicationListner를 등록해준다. 


```java
//@TransactionalEventListener 트랜잭션 이후에처리함
@Component
public class CustomSpringEventListener {
  
    @EventListener
    public void handleContextRefresh(CustomSpringEvent event) {
        ...
    }
}
```

#Publishing events
@EventListener로 주석 된 모든 메소드에 대해 void가 아닌 리턴 유형을 정의 할 수 있습니다. 특정 이벤트를 처리 한 결과로 null이 아닌 값을 반환하면 해당 결과가 새 이벤트로 전송됩니다. 이제 예전처럼 아래와같이 더이상 확장해서 불편하게 사용하지 않아도 된다.
```java 
public class CustomSpringEvent extends ApplicationEvent {
    private String message;
 
    public CustomSpringEvent(Object source, String message) {
        super(source);
        this.message = message;
    }
    public String getMessage() {
        return message;
    }
}
```
기존에 ApplicationEvent 이벤트를 정의하기위해서는 ApplicationEvent를 확장했어야하는데 좀더 유연하게 하기위해 이제 굳이 ApplicationEvent를 상속받지 않아도 된다. 위에서 받던대로 PayloadApplicationEvent 객체를 기반으로 랩핑된다.


# 트랜잭션 바운드
트랜잭션이 완료되면 해당 이벤트를 처리하게 할 수 있다. 

기존에 스프링은 컨텍스트가 트랜잭션을 인지하지 못하도록 구조화되어있었다. 그래서 오픈 인프라스르척를 만들었다. 트랜잭션 모듈은 새로운 @TransactionalEventListner 어노테이션을 찾는 EventlistnerFacotry를 구현한다. @TransactionalEventListner이 어노테이션을 사용하게되면 기본 리스너대신 트랜잭션이 완료됬을대 이벤트를 처리해주는 기능이 동작하게 되어진다.

@TransactionalEventListener는 일반 @EventListener이며, 기본값은 AFTER_COMMIT 인 TransactionPhase를 나타냅니다. 트랜잭션의 다른 단계 (BEFORE_COMMIT, AFTER_ROLLBACK, AFTER_COMMIT, AFTER_ROLLBACK의 별명 인AFTER_COMPLETION)를 연결할 수도 있습니다.

기본적으로 트랜잭션이 실행되고 있지 않으면 요청한 단계를 지키지 못하기 때문에 이벤트가 전혀 전송되지 않습니다. 그러나 @TransactionalEventListener에 fallbackExecution 속성이 있습니다. 이 어트리뷰트는 트랜잭션이 없으면 즉시 리스너를 호출하도록 Spring에 지시합니다.