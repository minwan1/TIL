## HMAC
### HMAC란?
해싱 기법을 적용하여 메시지의 위변조를 방지하는 기법을 HMAC (Hash-based Message Authentication) 이라고 한다. 좀더 상세히 설명하면 Sender와 Recipient는 서로 API를 주고 받아야한다. 그럴때 이메시지가 Sender 가보낸 메시지인지 인증을 해야한다. 뿐만 아니라 이 메시지가 변조가 되었는지 확인을 해야한다. 이러한 문제점을 해결하는것이 HMAC이다. Sender는 메시지를 보내기전에 공유된 Secret키와 메시지를 기반으로 해쉬값을 만든다. 그리고 이해쉬값을 헤더값에 포함하여 전달해준다. 그리고 Recipient는 그 받은 메시지를 기반으로 또 자신이 가지고있는 공유된 Secret를 이용하여 해쉬를 만들어내고 헤더값에 넘어온 키를 대조한다. 이것이 일치하면 Sender에대한 인증, 메시지의 무결성을 확인할 수 있다.

만약 중간에 해커가 메시지를 가로챈다고 하더라도 시크릿키를 알수 없기때문에 메시지를 변조할 수 없다. 그리고 보통 메시지 Body값에는 Timestamp를 넣는것이 일반적이다. 이렇게하면 서버에서 호출시간을 알 수 있고 이렇게 함으로써 좀더 보안성을 높여 사용할 수 있다. 아래는 HMAC의 예제 그림이다.

![](https://i.imgur.com/a5IsX06.jpg)

### HMAC Example(Java)
다음은 자바 소스를 이용한 예제이다. 다음은 SHA-256알고리즘을 이용하여 해쉬를 만든것이다. 그안에서 인코딩방법*  16진수방법과 base64 방법이 존재한다.

```java
public class HMACAuthentication {

    public void verifySignature(String secret, String payload, String signature) {
        try {
            final String resultHex = getHexHash(secret, payload);
            if (!resultHex.equals(signature))
                throw new RuntimeException("HMAC does not match.");
        } catch (Exception e) {
          throw new RuntimeException(e);
        }
    }

    private String getHexHash(String secret, String payload) throws NoSuchAlgorithmException, InvalidKeyException {
        final String hmacSHA256 = "HmacSHA256";
        final Mac hasher = Mac.getInstance(hmacSHA256);
        hasher.init(new SecretKeySpec(secret.getBytes(), hmacSHA256));
        final byte[] hash = hasher.doFinal(payload.getBytes());

//            String resultBase = DatatypeConverter.printBase64Binary(hash); // to base64
        return DatatypeConverter.printHexBinary(hash).toLowerCase(); // to hex
    }


}
```

참고
* [DOG발](http://sunphiz.me/wp/archives/tag/hmac)
