## Spring-JPA-OSIV

### 준영속 상태와 지연로딩
일반적으로 서비스와 레파지토리 계층에서는 영속성 컨텍스에 속하고 컨트롤러나 뷰단에서는 비영속상태가 된다. 따라서 뷰나,컨트롤러에서 변경감지나, 지연로딩이 작동하지 않는다.그럼으로 컨트롤러나 뷰에서 객체그래프같은 기능을 사용하게되면 익셉션이 발생한다. 만약 하이버네이트 구현체를 사용한다면 org.hibernate.LazyInitializationException예외가 발생한다. 이러한 문제를 해결하게 나온것이 OSIV이다.

### OSIV란?
Spring Boot에서 Default로 설정해주고 있는 Open Session In View Pattern에 대한 내용이다. 해당 패턴은 객체-관계 매핑(ORM, Object-Relational Mapping)의 사용으로 등장하게 된 패턴이다. Open Session In View에 대해 미리 정리를 하자면, Open Session In View는 영속성 컨텍스트를 뷰 렌더링이 끝나는 시점까지 개방한 상태로 유지하는 것입니다. 따라서 뷰에서도 지연로딩을 사용할 수 있게한다.



참고문헌
* [개발노트 - kingbbode](http://kingbbode.tistory.com/27)
