1. 일부 속성의 직렬화를 피하기위해 @xmlTransient and @JsonIgnore등을 안사용해도 된다.
2. 자원을 업데이트하거나 만들때 내가 원하는 속성만을 받을 수 있다.
3. 만약 스웨거를 사용한다면 @ApiModel 그리고 @piModelProperty 등의 특징들을 쓸 수 있다.
4. API에 버전에 맞는 다른 DTO들을 가질것이다.
5. 클라이언트에게 제공하고싶지 않은 데이터들을 안넘길 수 있다.

[DTO위키](https://en.wikipedia.org/wiki/Data_transfer_object)


Member 회원가입 컨트롤러 코드를 작성하면 다음과 같이 될 수 있겠습니다.