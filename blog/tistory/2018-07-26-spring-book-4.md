# 온라인 서점 API 만들기로 살펴보는 Spring Boot OOP 4
# Step-04 회원가입 Service 만들기 : 응용영역 효율적으로 관리하기 - Part1

# 응용 영역(서비스)

이 단계에서는 회원가입 기능을 구현하면서 `응용 영역`을 어떻게 하면 응집력 있고, 변경에 유연한 코드를 작성할지에 대해 알아볼 것입니다. 크게 아래와 같은 주제로 `응용영역`을 효율적으로 관리하는 방법에 대해 알아보겠습니다. (**소프트웨어에서 변경이 유연하고, 확장가능한 코드는 아주 핵심적인 부분입니다.**)

* 서비스크기
* 요구사항 정리 및 초난감 회원가입 Service 구현
* 캡슐화

# 1. 서비스크기

드디어 회원가입을 하기 위한 signUp 서비스를 구현할 차례입니다. 먼저 회원가입 서비스 기능을 구현하기 전에 회원가입 서비스를 어느 정도의 서비스 크기로 가져갈지 생각해야합니다.


## 도메인의 모든 기능을 한클래스에서 구현하는 방법
먼저 책이나 일반적으로 많이 사용하는 방법인 도메인의 모든 기능을 한 클래스에 서비스에서 구현하는 방법이 있습니다.

```java
// MemberService.Class

@Autowired
private MemberRepository memberRepository;
// 유저에관한 부가적인 기능을 만들기위한 수 많은 의존성 주입들
...


public Member signUp(final MemberSignupRequest request){ // 회원가입을 위한 member정보
    //비지니스로직
}
// 유저에 관한 기능들

```

**장점** :
* 하나의 클래스에 도메인과 관련된 모든 기능들이 모여 있다.
* 1인터페이스, 1구현체로 가져가게 되면 모든 Member의 기능들이 인터페이스에 명세되는 장점 있을 수 있다.

**단점** :
* 한 클래스에 너무 많은 기능들이 모여있어, Member 관한 모든 private 함수들이 뒤섞여 소스 분석이 힘들다.
* 관련 없는 너무 많은 의존성들이 하나에 클래스에 주입된다.
* 한 클래스 모든 테스트코드를 작성해야 하기 때문에 테스트 코드를 관리하기 힘들다.
* 결국 추상화되기 위해서는 특정 기능의 서비스로 분리될 수밖에 없다.
* SOLID 원칙을 준수하기 위해서는 결국 메서드를 분리해낼 수밖에 없다.(이 부분은 후에 좀 더 세부적으로 설명하겠습니다.)


## 구분되는 기능별로 서비스 클래스를 구현하는방법
그리고 다른 방법은 유저 도메인의 구분되는 기능별로 서비스 클래스를 구현하는 방법이 있습니다.
```java
@Service // 어노테이션을 붙이면 스프링에서 ComponentScan을 통해서 스프링 컨테이너에 자동으로 빈을등록해줌.
@AllArgsConstructor // 모든 필드 변수에 대해 생성자를 만들어줌으로 모든멤버필드에 스프링 빈이 주입이 되어진다.
public class MemberSignUpService {

    //회원가입에 필요한 의존성만 주입됨
    private final MemberRepository memberRepository;
    private final MemberHelperService memberHelperService;

    public Member signUp(final MemberSignupRequest request){
        //회원가입 로직
    }

}
````
**장점** :
* 유지 보수를 할 때 구분되는 기능별로 클래스가 나누어져 있기 때문에 해당 비즈니스로직에만 신경 쓸 수 있다.
* 해당 기능에 맞는 필요한 의존성만 주입할 수 있다.
* 테스트 코드 작성하기가 쉽다.
* 해당 기능을 추상화하려고 해도 이미 하나의 타입으로 뽑아져있기 때문에 추상화하기가 쉽다.

**단점** :
* 클래스가 많아진다.(단점일까... 처음엔 단점이라고 생각했는데 요즘들어 그렇게 단점은 아니라고 생각합니다.)


### 구분되는 기능별로 서비스 클래스를 구현하는방법 선택 이유

아래와 같은 이유로 `도메인의 모든 기능을 한 클래스 서비스`에서 구현하는 방법이 가지는 단점 때문에 `구분되는 기능별로 서비스` 클래스를 구현하는 방법을 선택해서 구현할 것입니다.

1. **테스트코드들을 구분하기 어려워진다.**

한 클래스에 너무 많은 기능들이 있기 때문에 이것을 한 클래스에 테스트 코드를 작성하게 되면 테스트를 구분하기가 어려워집니다. 물론 테스트 클래스만 여러 개로 나눌 수 있지만 이 또한 어떤 테스트 클래스에 어떤 테스트가 작성되었는지 찾기가 어렵습니다.
만약 `구분되는 기능별로 서비스 클래스를 구현하는 방법`으로 작성했다면 테스트 코드 또한 기능별로 나누어져 있기 때문에 쉽게 테스트 코드를 찾고 관리할 수 있습니다.

2. **너무 많은 public 함수들과 private 함수들이 뒤엉켜 소스분석하기가 어렵고, 한클래스에 너무많은 의존성 주입이 된다.**

너무 많은 public 함수들과 private 함수들이 뒤엉켜 소스 분석하기가 어려워집니다. 만약 `구분되는 기능별로 서비스 클래스를 구현하는 방법`으로 구현한다면 public 함수 그리고 private 함수가 모두 해당 기능에 관련 있는 함수들입니다. 뿐만 아니라 한 클래스에 너무 많은 의존성 주입이 되는 것을 막을 수 있습니다.

3. **결국 추상화되어지기 위해서는 특정 기능의 서비스로 분리될수밖에 없다.**

예를 들어 회원 패스워드 변경 기능, admin에 의한 패스워드 변경 기능이 있다고 해보겠습니다. 좀 더 자세히 설명하면 회원이 패스워드를 변경하기 위해서는 자신의 패스워드를 인증해야 하고 Admin은 별다른 인증 없이 회원의 패스워드를 변경할 수 있습니다. 물론 if 문으로 어드민인지 확인하고 처리할 수 있지만 계속해서 다른 역할들이 추가 될 수 있습니다. 그렇게 되면 패스워드 변경을 추상화해서 관리하는 것이 효율적일 것입니다.

하지만 여기에서 MemberService 자체를 추상화시키면 패스워드 변경을 제외한 나머지 기능들은 추상화가 되지 않을 것입니다. 이것은 ISP 위반입니다.(ISP에 대해서 나중에 좀 더 자세히 알아보겠습니다.) 결국 패스워드 변경을 추상화하기 위해 `도메인의 모든 기능을 한 클래스`에서 구현하는 방법의 MemberService.class에서 분리해서 별도의 클래스로 분리하고 인터페이스를 만들어야 합니다. 반면 `구분되는 기능별로 서비스 클래스`를 구현하는 방법 기능은 이미 클래스로 분리돼 있기 때문에 쉽게 유저 패스워드 변경이라는 인터페이스에 녹아들 수 있습니다.

# 2. 요구사항 정리 및 초난감 회원가입 Service 구현

## 요구사항 정리의 필요성

서비스 영역을 구현할 때 중요한 것은 요구 사항을 받고 요구 사항을 정리하는 것입니다. 요구 사항이 간단해 보여도 정리해야 하는데 그 이유는 아래와 같습니다.

* 간단한 작업 같지만 간단하지 않을 수 있습니다. 막상 요구 사항을 정리하다 보면 생각했던 것보다 작업량이 많을 수 있고 또 미쳐 생각하지 못한 곳에 영향을 주는 작업일 수 있기 때문입니다.
* 간단하다고 생각하고 정리하지 않고 바로 개발을 하게 되면 놓치는 부분이 발생합니다.
* 객체의 책임들을 나눌 수 있는 좋은 명세가 될 수 있습니다.


다음은 회원가입 기능을 구현하기 위한 요구 사항입니다.(실제 구현에서는 모바일 인증코드는 없을 예정입니다. 몇 가지 예제를 설명하기 위해 예제에만 넣었습니다.)

**회원가입 요구사항**

1. 유저를 가입시키는 기능을 구현한다.
2. 가입을 하려면 모바일 인증을 해야 한다.
3. 중복된 이메일은 회원을 가입할 수 없다.

## 초난감 회원가입 서비스 구현

```java

@Service
@AllArgsConstructor
public class MemberSignUpService {

    private final MemberRepository memberRepository;
    private final CodeVerificationService codeVerificationService;

    public Member signUp(final MemberSignupRequest request){

        final Member duplicator = memberRepository.findByEmail(email);
        if(duplicator != null) throw new MemberDuplicationException();

        final CodeVerification codeVerification = codeVerificationService.findByMobile(request.getMobile()); // 유저가 있는지 여부를 확인할 수 있는데 인증코드 정보 객체

        if(codeVerification.getAuthCode().equals(request.getAuthCode))){
            Member member = request.toMember();//성공
            memberRepository.save(member);
            return member;
        }else{
            throw new MobileAuthenticationCodeFaildException();
        }
    }
}

```

위 소스의 흐름은 이렇습니다.
1. 먼저 이메일이 중복되었는지 Email로 멤버를 조회하여 멤버의 존재 여부를 확인합니다.
2. 유저가 모바일 인증한 정보의 객체를 가져옵니다.
3. 유저의 모바일 정보가 인증되었는지 확인을 합니다.
4. 성공했다면 회원가입을 시킵니다.



다음 소스는 회원가입 기능을 하는 데는 문제가 없는 소스입니다. 하지만 코드의 가독성도 떨어지고, 응집력이 떨어지고, 변경에 아주 취약한 소스입니다. 그 이유는 클래스의 역할 분담이 제대로 되지 않았고, 객체의 캡슐화가 되지 않았기 때문입니다.

여기에서 말하는 역할 분담은 책임을 의미합니다. 책임을 알아보기 전에 먼저 캡슐화에 대해 알아보겠습니다.

# 3. 캡슐화

객체지향은 기본적으로 캡슐화를 통해 한 곳의 변화가 다른 곳에 미치는 영향을 최소화하여 변경의 유연함을 가질 수 있게 합니다. 캡슐화를 통해 응용영역을 좀 더 변경에 유연하게 만들 수 있습니다.

> - 캡슐화는 객체가 내부적으로 기능을 어떻게 구현하는지를 감추는것이다. 이를 통해 내부적 변경의 유연함을 주고 코드의 응집력을 높여주는 기법이다.

### 회원가입 예제코드의 문제점
캡슐화가 안되어있다는 게 무슨 말인지 먼저 알아보겠습니다. 예를 들어 패스워드 변경 기능을 위해서는 codeVerificationService에 의존하고 뿐만 아니라 다른 곳곳에서도 codeVerificationService를 사용하고 있다고 해보겠습니다. 그런데 회사 보안 정책 강화로 인증코드를 체크할 때 만료시간 체크 기능을 추가하라는 요구 사항이 들어왔습니다.

이렇게 되면 애플리케이션 전체에 모바일 인증을 하는 소스를 아래와 같이 인증코드 체크, 만료 여부 체크 기능을 할 수 있는 로직으로 변경해야 합니다.

```java
//PasswordChangeService
public void changePassword(final String mobile, final String authCode, final String password){
    CodeVerification CodeVerification = codeVerificationService.findByMobile(mobile);
    if(codeVerification.getAuthenticationCode.eqauls(authCode) && codeVerification.getExpireDate() < 현재시간){
        //성공
    }else{
        //실패
    }
}

```

만약 한 곳이라도 실수로 변경하지 않는다면 버그로 이어지게 됩니다. 운 좋게 버그 없이 이 문제를 해결하였다고 생각해보겠습니다. 그런데 갑자기 회사에서 사용자 UX를 생각해서 만료 체크 기능을 없애고 다시 원래 로직으로 변경하라는 요구를 했습니다. 이렇게 되면 다시 전방위로 소스를 수정해야 합니다. 문제는 그 사이에 모바일 인증코드가 다른 곳에 더 추가돼 있을 수 있습니다. 이렇게 되면 소스를 변경하는 과정에서 더욱더 버그가 날 확률이 커집니다. 단지 인증코드 정책 하나가 변경하는것인데 엄청난 리소스가 필요하고 전방위적으로 버그 리스크를 가져야합니다.

이러한 문제는 getter에 오용과 데이터 중심적인 사고로 인해 발생하는 문제입니다. 만약 좀 더 객체지향적인 프로그래밍을 생각했었으면 이러한 문제를 만들지 않고 CodeVerification 객체 자체에 인증처리에 관한 캡슐화된 기능을 제공했었을 것입니다.


## CodeVerification 객체 캡슐화하기
위에 각각의 서비스들에서 최종적으로 하고 싶은 것은 CodeVerification 객체를 통해 인증코드가 인증될 수 있는 인증코드인지 확인하는 것입니다. 내부적인 로직은 알 필요가 없습니다. 이 내용을 바탕으로 CodeVerification 객체에 verify라는 캡슐화된 메서드를 추가할 것입니다.

```java
public class CodeVerification{
    ... //인증객체 다른 변수

    private Timestamp expireDate;
    private String authCode; // 어떤시스템으로부터발급되어진 인증코드

    private boolean isExpired(){
        //expire check logic
    }

    private boolean isValidAuthCode(final String authCode){
      //auth code check logic
    }

    public void verify(final String authCode){ // 클라언트로부터 입력될수있는 인증코드

        // 인증코드가 만료되거나 인증코드가 맞지않으면 Exception

    }
}

```

이제 모든 서비스 영역들은 더 이상 모바일 인증 정책에 대해 알 필요 없이 CodeVerification에 캡슐화된 verify() 함수만 사용하면 됩니다. 예를 들어 위에서 작성한 패스워드 변경 서비스는 아래와 같이 사용할 수 있을 것입니다.

```java

//PasswordChangeService
public void changePassword(final PasswordChangeRequest request){
    CodeVerification CodeVerification = codeVerificationService.findByMobile(request.getMobile());
    codeVerification.verify(request.getAuthCode);
    //패스워드 변경로직
}


```
이제 회사에서 인증 코드에 정책이 변경된다고 해도 쉽게 그 변경에 대응할 수 있습니다. CodeVerification 객체에서만 캡슐화된 verify() 로직을 변경하면 되기 때문입니다. 객체의 캡슐화를 통해 코드의 응집력을 높이고 변경에 유연한 코드를 얻게 되었습니다.


## Tell, don't ask

`Tell, don't ask`는 객체지향 및 캡슐화를 잘 표현하는 문장 같습니다. 응용영역에서 특별한 경우를 제외하고는 객체한테 묻지 말고 시키는 것이 코드의 응집력을 높입니다. 그렇지 않으면 위와 같이 변경하기 어려운 코드가 만들어지기 때문입니다.

위의 `초난감 회원가입 서비스` 같은 문제를 만들지 않기 위해 항상 비즈니스로 직을 작성하면서 getter를 오용하여 데이터 중심적인 비즈니스 로직을 작성하고 있는지를 의심해봐야 합니다.

또한 객체지향적인 프로그래밍을 하기 위해서 항상 자신이 객체한테 묻고 있는지, 시키고 있는지를 생각하면서 개발해야 합니다. 그래야 좀 더 객체지향적 프로그래밍을 할 수 있기 때문입니다. (물론 비즈니스 로직이 엄청 커지거나, 공통으로 처리되지 않는 로직으로 인한 서비스 로직에서 비즈니 스로직을 작성해야 할 때도 있습니다.)


# 마치며

이장에서 응용 영역을 효율적으로 관리하기 위해 `구분되는 기능별로 클래스`를 구현하는 방법을 선택하여 `초난감 회원가입서비스`를 구현했습니다. 하지만 완성된 `초난감 회원가입 서비스`는 변경에 취약하고 응집력이 떨어지는 코드였습니다. 이렇게 변경에 취약한 이유는 많은 이유가 있겠지만 가장 기본적인 캡슐화가 되어 있지 않았습니다. 캡슐화를 통해 코드의 응집력을 높이고 변경에 유연한 코드로 변경하는 작업을 했습니다.

하지만 `초난감 회원가입 서비스`는 캡슐화의 문제도 문제이지만 기능의 역할 분담이 제대로 되지 않은 문제가 더 큽니다. 이 말은 책임이 적절하게 분리되지 않았다는 것을 의미합니다. 이러한 문제는 다음 part에서 좀 더 세부적으로 알아보겠습니다.
