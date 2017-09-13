## CASCADE
영속성 전이
특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들고 싶다면 영속성 전이 기능을 사용하면 된다.
JPA의 CASCADE옵션으로 영속성 전이를 제공한다. 쉽게 말해서 영속성 전이를 사용하면 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장할 수 있다.


http://wonwoo.ml/index.php/post/1002




개발도중 PersistentObjectException: detached entity passed to persist 라는 에러메세지를 만나게 되었다.

JPA 사용시 자동으로 생성되는 값을 가진 필드에 직접 값을 할당해 저장하고자 할 때 발생되는 에러다.

Self Join 도메인에서 @ManyToOne 의 옵션으로 Cascade 를 CascadeType.All 로 지정했는데 이 때문이었다.



CascadeType의 종류에는 다음과 같은 것들이 있다.

CascadeType.RESIST – 엔티티를 생성하고, 연관 엔티티를 추가하였을 때 persist() 를 수행하면 연관 엔티티도 함께 persist()가 수행된다.  만약 연관 엔티티가 DB에 등록된 키값을 가지고 있다면 detached entity passed to persist Exception이 발생한다.
CascadeType.MERGE – 트랜잭션이 종료되고 detach 상태에서 연관 엔티티를 추가하거나 변경된 이후에 부모 엔티티가 merge()를 수행하게 되면 변경사항이 적용된다.(연관 엔티티의 추가 및 수정 모두 반영됨)
CascadeType.REMOVE – 삭제 시 연관된 엔티티도 같이 삭제됨
CascadeType.DETACH – 부모 엔티티가 detach()를 수행하게 되면, 연관된 엔티티도 detach() 상태가 되어 변경사항이 반영되지 않는다.
CascadeType.ALL – 모든 Cascade 적용


참고
- [TaeHyun Jung](http://chomman.github.io/blog/java/jpa/programming/jpa-cascadetype-%EC%A2%85%EB%A5%98/)
