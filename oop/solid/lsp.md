

진짜 생각하는것 그이상으로 쪼개고 응집해야한다.


##


Don't ask tell me(제발...........)

만약 두개의 서버를 한곳에서 관리한다고 가정해보자


```java
@RestController
@RequestMapping("Cars")
public class CarController {

    @Autowired
    private AcarService acarService;
    @Autowired
    private BcarService bcarService;

    @Autowired
    private CarManager CarManager;

    @RequestMapping(value = "/a", method = RequestMethod.POST)
    public Car createCar() {
      return CarManager.createCar(acarService);
    }
    @RequestMapping(value = "/b", method = RequestMethod.POST)
    public Car createCar() {
      return CarManager.createCar(bcarService);
    }

}

public interface CarService {
  Car createCar(AcarService carService);
}

@Component
public class CarManager {
  public Car createCar(AcarService carService){
    carService.createCar();
  }
}

@Service
public class AcarService {
  public Car createCar(AcarService carService){
    //비지니스 로직
  }
}

@Service
public class BcarService {
  public Car createCar(AcarService carService){
    //비지니스로직
  }
}
```

여기에서 만약 bcarservice 의 경우 2차 패스워드 검사하는게 필요할경우 그것을 검증하는 SecondPasswordAuthentication class가필요하고, 그곳에서 틀린게 인증된경우 유저 2차패스워드 카운트를 넓혀라.. 그리고 그역할을 서비스에서 하지말아라.... userservice에게 그역할을 맡겨라... Don't akk tell me


##

객체지향 초기에 가장중요시 여기는 개념은 재사용성(Reusability) 이었지만, 지금은 워낙 시스템이 방대해지고 잦은 변화가 발생하다보니 유연성(Flexiblity)이 더 중요한 개념으로 여겨지고 있기 때문입니다.
