ApplicationContext와 BeanFactory 차이점
ApplicationContext는 Pre-loading을 하고 즉, 즉시 인스턴스를 만들고 BeanFactory는 lazy-loading을 하여 실제 요청 받는 시점에서 인스턴스를 만든다. 정리하자면 두 인터페이스의 차이점은 인스턴스화 시점이 다르다는 것이다.
