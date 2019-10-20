## Transaction Synchronization and Spring Application Events: Understanding @TransactionalEventListener


이것은  @TransactionalEventListner가 어떻게 작동하는지 설명과 @EventListner가 어떻게 다른지 알려줄것이다. 그리고 이것을 다루기전에 무엇이 위험한지 다룰것이다.

여기에서 주어진 예제는 주로 트랜잭션 동기화 이슈에대 다룰것이다. 


```java

@Service
public class CustomerService {
    private final CustomerRepository customerRepository;
    private final ApplicationEventPublisher applicationEventPublisher;
    public CustomerService(CustomerRepository customerRepository, ApplicationEventPublisher applicationEventPublisher) {
        this.customerRepository = customerRepository;
        this.applicationEventPublisher = applicationEventPublisher;
    }
    @Transactional
    public Customer createCustomer(String name, String email) {
        final Customer newCustomer = customerRepository.save(new Customer(name, email));
        final CustomerCreatedEvent event = new CustomerCreatedEvent(newCustomer);
        applicationEventPublisher.publishEvent(event);
        return newCustomer;
    }
}

```

```java
@Component
public class CustomerCreatedEventListener {
    private static final Logger LOGGER = LoggerFactory.getLogger(CustomerCreatedEventListener.class);
    private final TokenGenerator tokenGenerator;
    public CustomerCreatedEventListener(TokenGenerator tokenGenerator) {
        this.tokenGenerator = tokenGenerator;
    }
    @EventListener
    public void processCustomerCreatedEvent(CustomerCreatedEvent event) {
        LOGGER.info("Event received: " + event);
        tokenGenerator.generateToken(event.getCustomer());
    }
}
```

다음과 같이 호출한다면 위에 CustomerService와 같이 트랜잭션이 묶이는 문제가 있습니다.


## @TransactionalEventListener - transaction synchronization

@TransactionalEventListener를 이용하면 트랜잭션이 완료된이후에 eventlist가 진행되어진다. 


참고
https://dzone.com/articles/transaction-synchronization-and-spring-application


https://dzone.com/articles/annotation-driven-event-listeners-in-spring-42

