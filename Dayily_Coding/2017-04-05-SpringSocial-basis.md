# Daily Coding 2017-04-05일 spring social


## sprimg social을 이용한 로그인 구현

* spring social도 여러가지 방식이 있다 밑에 두가지만봐도 단순연결과 로그인 기능으로 토큰등을 받는 방법이 있다.
  * /signin/facebook
  * /connect/facebook

```java
@Controller
@RequestMapping("/")
public class HelloController {

    private Facebook facebook;
    private ConnectionRepository connectionRepository;

    public HelloController(Facebook facebook, ConnectionRepository connectionRepository) {
        this.facebook = facebook;
        this.connectionRepository = connectionRepository;
    }

    @GetMapping
    public String helloFacebook(Model model) {
        if (connectionRepository.findPrimaryConnection(Facebook.class) == null) {
            return "redirect:/connect/facebook";
        }

        model.addAttribute("facebookProfile", facebook.userOperations().getUserProfile());
        PagedList<Post> feed = facebook.feedOperations().getFeed();
        model.addAttribute("feed", feed);
        return "hello";
    }

}

```
* 로그인에 성공하게되면 connectionRepository에 커넥션정보가 저장된다.
* facebookConnect.html에서 로그인 버튼을 클릭하면 connectionRepository에 정보가 페이스북 로그인정보가 저장되고 아마 facebookConnected.html로 쏴주고 거기에서 버튼을 클릭하면 "/"루트로 보내서 페이스북 게시물 보여줌.
* hello.html로 다시 쏴주는듯


참고
* [가장 빨리 만나는 스프링 부트](http://storefarm.naver.com/sosobook/products/575628365?NaPm=ct%3Dj13jxq7c%7Cci%3Ddf62af99640375f2283b1abee1eefb6268635987%7Ctr%3Dsls%7Csn%3D421722%7Chk%3D141e7e343542920c7d3dfa4256b85462873a4dab)
* [Spring 4.0 프로그래밍](http://storefarm.naver.com/dcvirus/products/458328014?NaPm=ct%3Dj06r6ydk%7Cci%3D1744f23aa4586709889a372fc15683afa2b4928e%7Ctr%3Dsls%7Csn%3D182521%7Chk%3Dab9fe496302792c50421edea06a3e322286ad2b9) / 최범균 /
* [허원철](http://heowc.tistory.com/m/11)
* [spring.io](https://spring.io/guides/gs/accessing-facebook/)
