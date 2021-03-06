## 왜 토큰을 사용하게 돼었을까 ?

기존의 인증 시스템에서는 서버측에서 유저들의 정보를 기억하고있어야합니다. 이 세션을 유지하기 위해서는 여러가지 방법이 사용됩니다. 메모리/ 디스크/ 데이터베이스에 이러한데이터를 담ㄴㄴ다.

## JWT란
JSON Web Token (JWT) 은 웹표준 (RFC 7519) 으로서 두 개체에서 JSON 객체를 사용하여 가볍고 자가수용적인 (self-contained) 방식으로 정보를 안전성 있게 전달해줍니다. JWT 는 필요한 모든 정보를 자체적으로 지니고 있습니다. JWT 시스템에서 발급된 토큰은, 토큰에 대한 기본정보, 전달 할 정보 (로그인시스템에서는 유저 정보를 나타내겠죠?) 그리고 토큰이 검증됐다는것을 증명해주는 signature 를 포함하고있습니다.

JWT의 생김새
JWT는 .을 구분자로 3가지의 문자열로 구성되어있다 구조는 다음과 같다
```
aaaaaa.bbbbbb.cccccc
헤더,내용,서명
```
먼저 위 jwt의 생김새를 간단하게 설명하면 헤더,내용은 base64UrlEncode로 되어있고, 서명은 헤더+내용 + 암호화키를 SHA256등으로 해시화되어진 형태입니다. 일반적으로 이러한 작업들은 JWT토큰을 만들때는 JWT를 담당하는 라이브러리가 자동으로 인코딩 및 해싱 작업을 해준다. 아래에서 좀더 상세하게 이내용들을 다뤄볼 것이다.

## Header
header는 두가지의 정보를 지니고 있다.
Type : 토큰의 타입을 지정합니다. 기본적으로 JWT 가 설정일 될것이다.
alg : 해싱 알고리즘을 지정합니다. 해싱 알고리즘으로 보통 SHA256,RSA가 사용되며, 이알고리즘은 토큰을 검증할때 사용되는 signature부분에서 사용된다.

## 정보(payload)
Payload부분에는 토큰에 담을 정보가 들어있다. 여기에 담는 정보의 한 조각을 클레임(claim)이라고 부르고, 이는 name/value의 한쌍으로 이뤄져있다. 토큰에는 여러개의 클레임들을 넣을 수 있다. 클레임의 종류는 크게 3가지로 등록된 클레임, 공개 클레임, 비공개 클레임으로 이루어져 있다.

### 등록된(registered)클레임
등록된 클레임들은 서비스에서 필요한 정보들이 아닌, 토큰에 대한 정보들을 담기 위하여 이름이 이미 정해진 클레임들이다. 등록된 클레임의 사용은 모두 선택적이며, 이에 포함된 클레임 이름들은 다음과 같다.

| ㅁㄴㅇㅁㄴ | ㅁㄴㅇㄴㅁ                    |
| ---------- | ----------------------------- |
| iss        | 토큰발급자                    |
| sub        | 토큰제목                      |
| and        | 토큰 대상자                   |
| nbf        | 토큰의활성화날짜와 비슷한개념 |
| iat        | 토큰이 발급된 시간            |
| jti        | JWT의 고유식별자(중복여부확인 |

### 공개(public)클레임

공개 클레임들은 충돌이 방지된(collision-resistant)이름을 가지고 있어야 합니다. 충돌을 방지하기 위해서는, 클레임 이름을 URI형식으로 짓습니다.
```javascript
"https://test.com/jwt_claims/is_admin": true
```

### 비공개(private)클레임
등록된 클레임도 아니고, 공개된 클레임들도 아니다. 클라이언와 서버 합의하에 사용되는 클레임 이름들이다. 공개 클레임과는 달리 이름이 중복되어 될 수 있으니 사용할때유의해야함

```javascript
{
  "username":"test"
}
```
최종적으로 PayLoad는 아래와같이 구성될 수 있습니다.
예제 PayLoad
```javascript

{
    "iss": "test.com",
    "exp": "1485270000000",
    "https://test.com/jwt_claims/is_admin": true,
    "userId": "11028373727102",
    "username": "test"
}
```
위 예제는 payload는 2개의 등록된클레임, 1개의 공개 클레임, 2개의 비공개 클레임으로 이루어져있다.

### 서명(signature)
JSON WebToken 의 마지막 부분은 바로 서명이다. 이 서명은 헤더의 인코딩과, 정보의 인코딩값을 합친후 주어진 비밀키로 해쉬를 하여 생성한다.
서명 부분을 만드는 슈도코드의 구조는 다음과 같다.

```javascript
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

위에 내용들을 [https://jwt.io/](https://jwt.io/) 에서 직접 실습해볼 수 있다.
