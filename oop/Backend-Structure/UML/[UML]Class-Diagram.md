## Class Diagram

### Class Diagram이란?
Class Diagram은 UML의 한종류이다. UML은 프로그램 설계를 표현하기 위해 사용하는, 주로 그림으로 된 표기법을 의미한다. 객체지향 언어와 밀접한 관려이 있기에 객체지향 모델링 언어라고도한다. 소프트웨어 시스템, 업무모델링, 시스템의 산출물을 규정하고 문서화하는 언어이고 이 UML종류중 하나가 클래스 다이어그램이다. 클래스 다이어그램은 클래스들간의 관계를 보여주는 구조 다이어그램이다.

### 특징
클래스 다이어그램은 클래스 내부의 정적인 내용이나 클래스 사이의 관계를 표기하는 다이어그램으로 시스템의 일부 또는 전체의 구조를 나타낼수 있다. 클래스다이어그램은 클래스들의 관계를 쉽게 보고, 의존관계를 쉽게 파악하게 해준다.

### 클래스간의 관계
아래 그림은 클래스간의 관계를 표현할때 사용하는 표기법이다.

![](https://i.imgur.com/mtZAoJB.png)

**종류**
- Dependency (의존)
- Generalization (일반화)
- Realization (실체화)
- Association (연관)
  - Aggregation (집합 연관)
  - Composition (복합 연관)

위는 Class다이어그램에서 가장 많이사용되는 표기법이다.

#### 1. Dependency Relationship (의존관계)
Dependency는 클래스 다이어그램에서 일반적으로 제일 많이 사용되는 관계로서, 어떤 클래스가 다른 클래스를 참조하는 것을 말한다. 아래는 카카오 노티를 보내기 위해서는 카카오 서비스를 이용해야 하기 때문에 카카오 서비스에 의존하고 있는 그림이다. 아래의 그림은 Intelij의 Class Diagram으로 보면 점선과 화살표로 표현되는 것을 볼 수 있다.
![](https://i.imgur.com/Og7BM9s.png)
참조의 형태는 메서드 내에서 대상 클래스의 객체 생성, 객체 사용, 메서드 호출, 객체 리턴, 매개변수로 해당 객체를 받는 것 등을 말하며 해당 객체의 참조를 계속 유지하지는 않는다.

#### 2. Generalization (일반화 관계)
Generalization은 슈퍼(부모)클래스와 서브(자식)클래스간의 Inheritance(상속) 관계를 나타낸다. 여기서 Generalization이란 서브 클래스가 주체가 되어 서브 클래스를 슈퍼 클래스로 Generalize 하는 것을 말하고 반대의 개념은 슈퍼 클래스를 서브 클래스로 Specialize(구체화) 하는 것이다. 상속은 슈퍼 클래스의 필드 및 메서드를 사용하며 구체화 하여 필드 및 메서드를 추가 하거나 필요에 따라 메서드를 overriding(오버라이딩) 하여 재정의 한다. 또는 슈퍼 클래스가 추상 클래스인 경우에는 인터페이스의 메서드 구현과 같이 추상 메서드를 반드시 오버라이딩 하여 구현하여야 한다. 아래의 그림은 Intelij의 Class Diagram으로 보면 실선과 화살표로 표현 되는것을 볼 수있다.

![](https://i.imgur.com/l3Uz7Ur.png)


#### 3. Realization (실체화)
Realization은 interface의 spec(명세, 정의)만 있는 메서드를 오버라이딩 하여 실제 기능으로 구현 하는 것1을 말한다. 아래의 그림은 Intelij의 Class Diagram으로 보면 실선과 화살표로 표현 되는것을 볼 수있다.
![](https://i.imgur.com/9nrYUEx.png)

#### 4. Association (연관), Directed Association(방향성 있는 연관)
클래스 다이어그램에서의 Association은 보통 다른 객체의 참조를 가지는 멤버 필드를 의미한다. 아래 클래스 다이어그램은 두 가지 형태의 Association을 나타내고 있다.
![](https://i.imgur.com/YGwa7w7.png)
첫 번째 다이어그램은 일반적인 Association으로 단지 실선 하나로 클래스를 연결하여 표기하고 두 번째 다이어그램은 Directed Association으로 클래스를 실선으로 연결 후 실선 끝에 화살표를 추가한것이다. Association과 Directed Association의 차이는 화살표가 의미하는 navigability(방향성)인데 이것에 따라 참조 하는 쪽과 참조 당하는 쪽을 구분한다.

#### 5. Aggregation (집합 연관)
Aggregation은 Shared Aggregation이라고도 하며 Composition(Composite Aggregation)과 함께 Association 관계를 조금 더 특수하게 나타낸 것으로 whole(전체)와 part(부분)의 관계를 나타낸다. Association은 집합이라는 의미를 내포하고 있지 않지만 Aggregation은 집합이라는 의미를 가지고 있다.
![](https://i.imgur.com/5mfvp7k.png)
표기법은 위와 같이 whole과 part를 실선으로 연결 후 whole쪽에 비어있는 다이아몬드를 표기한다. Part쪽에는 화살표를 명시하여도 되고 명시하지 않아도 된다. 두 번째 다이어그램은 User에서 Address 쪽으로 화살표가 있으므로 User가 Address를 참조하는 것을 의미한다. Navigability가 없는 Association은 명시되지 않은 것으로 User가 Address를 참조할 수도, Address가 User를 참조할 수도, 또는 둘 다일 수도 있는 것을 의미한다. 그런데 코드를 보면 위에서 보았던 Association의 코드와 똑같다. Association과 Aggregation은 집합이라는 개념적인 차이는 있지만 코드에서는 이 차이를 구분하기 힘들다. Intelij 에서는 그냥 연관들은 복합연관으로 다처리하는 것 같다. 이유는 잘모르겠다.


#### 6. Composition (복합 연관)
Composition(또는 Composite Aggregation)도 Aggregation과 비슷하게 whole(전체)와 part(부분)의 집합 관계를 나타내지만 개념적으로 Aggregation보다 더 강한 집합을 의미한다.
![](https://i.imgur.com/OhRl7vZ.png)
Composition은 Aggregation보다 강한 집합이라고 했다. 여기서 강한 집합이란 part가 whole에 종속적이어서 part가 whole의 소유입니다. 반면 Aggregation은 part가 whole에 대해 독립적이어서 whole이 part를 빌려 쓰는 것과 비슷합니다. 이러한 의미 때문에 Aggregation과는 다르게 명확하게 나타나는 점이 있다

* 첫 번째, part를 가지는 whole 인스턴스가 part 인스턴스의 전체 수명을 책임진다.
* 두 번째, part에 해당하는 인스턴스는 공유 될 수 없다.


아래는 대부분의 다중성을 표현한 것입니다.

표기법 | 의미
------- | -------
0..1 | 0 또는 하나의 인스턴스. n..m 표기법은 n에서 m까지의 범위
0..* 또는 * | 0을 포함한 무한수. 제한없음
1 | 명백한 하나
1..* | 적어도 하나이상


출처: http://chanlee.tistory.com/entry/uml-practice-class [차니의 B로그]

### 결론
애플리케이션 규모가 커지면 커질수록 클래스 다이어그램을 이용해서 관리를 해야겠다는 생각이 점점 든다. Intelij Class Diagram 정도로만 관리를 해도 클래스 다이어그램은 클래스들의 관계를 쉽게 보고, 의존관계를 쉽게 파악하게 관리할 수 있을 것 같다. 그런데 Intelij에서는 Association과 관련하여 그냥 복합 연관으로 만 관리하는 것 같은데, 왜 그런지에 대해서는 좀 더 알아봐야 할 것 같다.

참고 사이트

* [킈킈킈!!](http://ysmanse.tistory.com/31)
* [Nextree](http://www.nextree.co.kr/p6753/)
* [_jbee](http://asfirstalways.tistory.com/95)
* [uml-practice-class](http://chanlee.tistory.com/entry/uml-practice-class)