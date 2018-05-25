JPA 기본


JPA 프레임워크로부터 reflection을통해 새로운 인스턴스를 만들어야기 때문에 기본 생성자가 필요하다.

//하이버네이트가 reflection을 통해서 동적프록시로  class<T>.newInstance()를 하기위해서는 기본생성자를 명시해주어야 인스턴스를 생성 할 수 가있다.
