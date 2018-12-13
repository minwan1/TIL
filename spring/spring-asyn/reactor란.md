## Reacotr란


Spring5에서부터는 java 8이상이여야 하고 servlet3.0 이상이여야 하는데 국내시장에서도 물론 많은 기업들이 java와 servlet버전을 올리고 있지만 기존 레거시 버전을 유지하려는 기업 또는 소프트웨어들이 많은 상태에서 과연 Spring5가 국내에서 얼마만큼 영향을 줄지는 미지수입니다. 

## Reactive Progrmaing 이란

리액티브 프로그래밍은 쉽게 말해 논블록킹 기반에 어플리케이션을 말하고 이것은 비동기와 이벤트 기반으로 작성된다. 또한 이것은 기존에 스레드 기반으포로그래밍이 아닌 소수의 스레드로 프로그래밍 하는것을 말한다. 또한 기존에 수직적인 즉 클러스터링을 통한 수평적인 서버확장이 아닌 JVM 내에서 수직 확장하는것을 말한다.


## Spring reactor란

Spring Reactor는 non-booking을 충분히 제공해주는 Reactive Streams라이브러리이며 많은 팀들이 조인하였는데 이중 spring.io가 눈에 보이네요





## MONO, FLUX
아주 쉽게 생각하면 자바에 Future 타입을 생각하면된다. 생성은 다음과 같이 하면된다. 다음과 같이 생성할 수 있다.
```java
Mono.just("Hello");
```

