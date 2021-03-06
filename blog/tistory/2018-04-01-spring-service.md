## 스프링 기능 추상화 예제

### 시작하며
이글의 내용은 [토비의 스프링](http://book.naver.com/bookdb/book_detail.nhn?bid=7006516)책의 내용으로 제가 이해한 스프링 서비스 추상화에 대한 내용을 정리한 글입니다. 물론 책에 더 상세하게 설명이 되어있지만 개인적으로 간단하게 정리하고 싶었습니다. 다른 사람에게 잘 설명을 할 수 있어야 더욱 깊게 이 내용을 이해한 것이라고 생각하기 때문입니다!! 물론 또 저만을 위한 것은 아니고 이 글을 통해 서비스 추상화에 대한 이해를 하시는데 도움이 되시는 분들이 있을 거라고 생각합니다! [참고로 책에서는 mybatis 예제였으나.. 이 글에서 Spring JPA로 구현한 예제입니다.]

### 추상화란
자바에는 표준 스펙, 사용 제품, 오픈소스를 통틀어서 사용방법과 형식은 다르지만 기능과 목적이 유사한 기술이 존재한다. 환경과 상황에 따라서 기술이 바뀌고, 그에 따른 다른 API를 사용하고 다른 스타일의 접근 방법을 따라야 한다는 건 매우 피곤한 일이다. 이러한 문제를 해결하기 위해서는 규격을 정하고 그것을 이용해야 한다. 그 규격은 인터페이스로 정할 수 있다. 예를 들어 List라는 인터페이스가 있으면 우리는 구현체를 ArrayList를 선언하느냐, LinkedList를 선언하느냐에 따라 구현체가 달라진다. 이렇듯 구현체를 어떻게 구성하느냐에 쓸 수 있는 기능, 효율이 달라진다. 다음 글에서 스프링이 어떻게 성격이 비슷한 여러 종류의 기술을 추상화하고 이를 일관된 방법으로 사용할 수 있도록 지원하는지 확인할 것이다. 이 글에서는 정책에 따른 유저 레벨[티어] 업그레이드를 추상화하는 방법에 대해 설명할 것이다. 좀 더 상세한 설명은 아래에서 설명할 것이다.

#### 요구사항
먼저 책에서 Level, User, UserService, UserRepository, 의 클래스가 있다. 이 클래스들을 이용해서 우리는 유저의 레벨 관리 기능을 추가해야 한다. 유저의 레벨 관리 기능은 관리자 호출 등을 통해서 userService.upgradeLevels()라는 메서드를 호출했을 때 이 애플리케이션의 존재하는 유저들이 아래의 요구 조건에 맞게 유저의 레벨을 올려야 한다는 요구 조건이다.

다음은 요구조건과 레벨이 올라가기 위한조건에 대한 내용이다.
* 사용자의 레벨은 BASIC, SILVER, GOLD 세가지중 하나이다.
* 사용자가 처음 가입하면 BASIC 레벨이 되며, 이후 활동에 따라서 한단계씩 업그레이드 될 수 있다.
* 가입 후 50회 이상 로그인을 하면 BASIC에서 SIRVER레벨이 된다.
* SILVER레벨이면서 30번 이상 추천을 받으면 GOLD레벨이 된다.
* 사용자 레벨의 변경 작업은 일정한 주기를 가지고 일관적으로 진행된다. userService.upgradeLevels()를 호출하기 전까지는 조건을 충족하더라도 레벨의 변경이 일어나지 않는다.

참고로 위의 기본 요구 조건은 정해진 조건에 따라 사용자의 레벨이 주기적으로 변경되거나 레벨을 업그레드하는 정책이 변경될 수 있다. 예를들어 연말 이벤트나, 새로운 서비스 홍보기간중에는 레벨 조건 정책이 바뀔 수 있다. 이럴때 어떻게 효율적으로 변경할 수 있을까. 그것은 바로 추상화이다. 여기에서 추상화해야 할것은 바로 UserLevelUpgradePolicy이다. 먼저 위의 요구 조건의 맞는 기능을 구현하고 이것을 어떻게 추상화하여 유연하게 바꿀지에 대해 생각해보자.

#### 객체선언
먼저 아래와 같이 요구조건에 맞는 객체와 서비스들을 선언하자.

##### User 객체
```java
@Getter
@Entity
@NoArgsConstructor
public class User {

    @Id
    private String id;
    private String name;
    private Level level;
    private int loginCount;
    private int recommend;


    @Builder
    public User(String id, Level level, int loginCount, int recommend, String name) {
        this.id = id;
        this.level = level;
        this.loginCount = loginCount;
        this.recommend = recommend;
        this.name = name;
    }

    public void updateName(String name) {
        this.name = name;
    }

    public void setLevel(Level level){
        this.level = level;
    }


    public void updateLevel(){
        final Level nextLevel = this.level.nextLevel();
        if(nextLevel == null){
            throw new IllegalStateException(this.level + "은 업그레이드가 불가능합니다.");
        }else{
            this.level = nextLevel;
        }
    }
}

```

##### 레벨 객체
```java
public enum Level {
    GOLD(3, null),
    SILVER(2, GOLD),
    BASIC(1, SILVER),
    ;

    private final int value;
    private final Level next;

    Level(int value, Level next){
        this.value = value;
        this.next = next;
    }

    public int value(){
        return value;
    }

    public Level nextLevel(){
        return this.next;
    }
}
```
관리자가 아래의 기능을 호출하면 애플리케이션에 모든 유저들의 레벨이 레벨정책조건에 따라 업데이트가 된다.
##### UserService 객체
```java
// userService

@Transactional
public void upgradeLevels(){
    List<User> users = (List<User>) userRepository.findAll(); // 모든 유저 조회
    for(User user : users){ // 유저하나하나 조회
        if(canUpgradeLevel(user)) // 업그레이드 가능여부
            user.updateLevel(); // 업그레이드
    }
}

private boolean canUpgradeLevel(User user){// 레벨 업그레이드 가능한지 여부를 체크하는 함수이다.
    Level currentLevel = user.getLevel();
    switch (currentLevel){
        case BASIC: return (user.getLoginCount() >= MIN_LOGCOUNT_FOR_SILVER); //로그인한횟수
        case SILVER: return (user.getRecommend() >= MIN_RECCOMEND_FOR_GOLD); // 추천횟수
        case GOLD: return false;
        default: throw new IllegalArgumentException("Unknown Level");
    }
}
```
여기까지 간단하게 기능을 구현했다. upgradeLevels메소드를 호출하면 모든 유저를 조회해서 요구조건에 맞게 레벨업을 시킨다. 지금은 업그레이드 가능여부를 로그인 카운트와 추천 카운트를 기반으로 유저의 레벨업을 시켰다. 하지만 위에서 말한대로 연말 이벤트나 새로운 서비스 홍보 기간중에 레벨업그레이드 정책이 달라질 수 가 있다. 물론 현재 canUpgradeLevel메소드를 지우고 새롭게 코드를 짜는 방법도 있다. 하지만 이벤트성 레벨업그레이드는 한번만 사용하거나 그 비슷한 이벤트가 일어나야 다시 사용할 수 있다. 그래서 이런 부분을 부품처럼 잘라 내서 관리 할수 있다.
```java
public interface UserLevelUpgradePolicy {
    boolean canUpgradeLevel(User user);
}
```

이렇게 레벨 업그레이드 정책기능을 인터페이스로 만들어 필요한 정책에 따라 의존성 주입을해서 사용할 수 있다. 예를 들어 아래와 같이 사용할 수 있다. 아래 보면 UserLevelUpgradePolicy라는 인터페이스를 선언했다. 그리고 userService에서 그 인터페이스를 이용해 정책에 따라 구현 Component를 주입받아 레벨을 업그레이드한다.

```java
//userService
@Autowired
private UserLevelUpgradePolicy userLevelUpgradePolicy;

@Transactional
public void upgradeLevels(){
    List<User> users = (List<User>) userRepository.findAll(); // 모든 유저 조회
    for(User user : users){ // 유저하나하나 조회
        if(userLevelUpgradePolicy.canUpgradeLevel(user)) // 업그레이드 가능여부
            user.updateLevel(); // 업그레이드
    }
}
```

이제 일반적인 업그레이드 정책이냐, 이벤트에 정책이냐에 따라서 아래와 같이 정책을 사용할 수 있다.
```java
public class OrdinaryLevelUpgradePolicy implements UserLevelUpgradePolicy{

    @Override
    public boolean canUpgradeLevel(User user) {
        Level currentLevel = user.getLevel();
        switch (currentLevel){
            case BASIC: return (user.getLoginCount() >= MIN_LOGCOUNT_FOR_SILVER);
            case SILVER: return (user.getRecommend() >= MIN_RECCOMEND_FOR_GOLD);
            case GOLD: return false;
            default: throw new IllegalArgumentException("Unknown Level");
        }
    }
}

public class EventLevelUpgradePolicy implements UserLevelUpgradePolicy{
    @Override
    public boolean canUpgradeLevel(User user) {
        //이벤트성 비즈니스로직
    }
}

```
레벨정책에 따라 아래와같이 의존성 주입을 해줄 수 있다. 그렇게되면 일반적인 정책과 이벤트 진행에따라 정책을 유연하게 변경할 수 있다.
```java
@Bean
public UserLevelUpgradePolicy userLevelUpgradePolicy(){
      return new OrdinaryLevelUpgradePolicy();
  }
```
### 마무리하며
여기까지 아주간단하게 스프링에서 어떻게 추상화를 사용할지에 대해 간단히 구현해봤다. 이렇게 하면 어떤 정책클래스를 주입받았느냐에 따라서 유저 레벨을 업그레이드할 수 있다. 물론 그 구현체 클래스에 비즈니스로직에 따라서 업그레이드를 할 수 있느냐 못하느냐가 결정된다. 위에서 구현한 레벨업그레이드 정책클레스에서는 워낙 비즈니스로직이 간단해서 저렇게까지 하나 이런생각이 들 수 있다. 하지만 정책이 더 생기고, 비즈니스 로직이 아주 복잡하다고 생각하면 이렇게 추상화해서 사용하는것은 유지보수, 확장성면에 아주 큰도움이 될것이다. 또한 이렇게 구성함으로 SOLID 원칙에서 말하는 원칙중 하나인 단일책임의 원칙을 아주 잘 지킬 수 있다.

위와같이 UserLevelUpgradePolicy를 인터페이스로 뺌으로써 어떤 정책이 들어와도 적절하게 책임과 관심이 다른 코드로 분리된다. 또한 애플리케이션 로직과 기술/환경을 분리하는 등의 작업을 통해 어떤 변경이 필요할 때 수정 대상이 명확해진다.

추상화를 통해 인터페이스를 도입하고 이를  DI 를 통해 연결하고 그로 인해 개방 패쇄 원칙도 잘 지키고 모듈 간에 결합도가 낮아서 서로의 변경이 영향을 주지 않고, 같은 이유로 변경이 단일 책임에 집중되는 응집도가 높은 코드가 나오게 되었다.

예제소스는 아래에서 참고할 수 있습니다.

[git-hub-예제소스](https://github.com/minwan1/Spring-tobi/tree/service-abstraction-level-policy/5-service-abstraction)


참고
* [토비의 스프링](http://book.naver.com/bookdb/book_detail.nhn?bid=7006516)
