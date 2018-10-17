
read committed 격리수준

# Isolation 전략




## READ_UNCOMMITTED
커밋되지 않은내용(flush는 이루어져있어야함.)을 조회하면 조회가 된다.

## READ_COMMITTED (기본 속성으로 가져가잔다)
커밋되지 않은 내용은 조회되지 않는다. 하지만 A트랜잭션을 통해  1유저정보를 조회하고 작업하고있던도중 B트랜잭션이 2유저정보를 수정해서 커밋했다. 그리고 다시 트랜잭션 1이 유저정보를 조회한다면 해당 정보는 변경되어있다. 이것을 NOT-REPEATABLE READ라고한다

// 애플리케이션 레벨에서 영속성 컨텍스트에 의해 REPEATABLE READ 여기까지 커버 가능하다. 물론 스칼라갓을 바로조회하면 지원을 안하겟지만 말이다.
// Spring jpa에서는 영속성컨텍스트, 캐시등으로 findAll까지도 NOT-REPEATABLE READ는 발생하지 않는다.
// 팬텀읽기까지는 발생한다. 

//퀀텀읽기는 발생한다.

```java
    @Transactional(isolation = Isolation.READ_COMMITTED)
    public Member findById(final long id){
        final List<Member> all = memberRepository.findAll();//1명유저
        try {
            Thread.sleep(20000);
        }catch (Exception e){

        }
        final List<Member> all1 = memberRepository.findAll();//2명유저
        if(member == null) throw new MemberNotFoundException();
        return member;
    }
```



```java
    @Transactional(isolation = Isolation.READ_COMMITTED)
    public Member findById(final long id){
        final List<Member> all = memberRepository.findAll();//1명유저
        try {
            Thread.sleep(20000); 
            //이중간에 유저 정보를 변경(UPDATE)한다.
        }catch (Exception e){

        }
        // 1명유저
        final List<Member> all1 = memberRepository.findAll();//영속성 컨텍스트에 의해 처음에 조회했던 all과 같은 정보를 조회함
        //만약 위에 all 조회 기능이없다면 영속성컨텍스테에 변경된 유저정보가 없기 때문에 디비로부터 조회해오기때문에 변경된 유저정보가 조회된다.
        
        if(member == null) throw new MemberNotFoundException();
        return member;
    }
```

## REPEATABLE READ
한번 조회한 데이터는 이제 다시 조회해도 변경되지 않는다. 하지만 트랜잭션 A가 10살이하의 회원을 조회했는데, 트랜잭션 B가 갑자기 회원하나를 추가했다. 그리고 다시 트랜잭션 a가 10살이하의 사람을 조회하면 회원이 하나 추가상태로 조회된다. 이처럼 반복조회시 데이터가 달라지는것을 PHANTOM READ라고한다.

//  팬텀읽기 발생
## SERIALIZABLE 
가장 엄겨한 트랜잭션 격리 수준이다. 여기서는 PHANTOM READ도 발생하지 않는다.


```java
    @Transactional(isolation = Isolation.SERIALIZABLE)
    public Member findById(final long id){
        final List<Member> all = memberRepository.findAll();//1명유저
        try {
            Thread.sleep(20000); 
            //이중간에 다른 트랜잭션이 접근하려고하면 접근을 못하고 이 트랜잭션이 끝나고 insert가 수행되어짐
        }catch (Exception e){

        }
        final List<Member> all1 = memberRepository.findAll();//1명유저 조호됨
        if(member == null) throw new MemberNotFoundException();
        return member;
    }
```



