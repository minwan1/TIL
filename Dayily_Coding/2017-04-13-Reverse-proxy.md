# Daily Coding 2017-04-11일

## Gson parse
```java
User user  = new User();

String str = gson.toJson(user);
JsonParser parser = new JsonParser(); //parser생성
JsonObject tempjson = parser.parse(str).getAsJsonObject(); //json으로 변경되는부분
tempjson.addProperty("credentialsNonExpired", true);
json.add("userInformation", tempjson); //json으로 바로들어가진다. 위에서 parese가 됬기 때문이다.

```

http는 포트 80이 생략
htts(http+ssl임)는 443이 생략됨
엔진엑스에서 톰켓으로 reverse proxy 설정으로 세션유지 가능[설명](https://www.iancollington.com/nginx-as-a-reverse-proxy-in-docker/)

```
UserIdSource에 대한 설명, (간단하게 유니크한값을 저장하는듯 userconnection에)
```
[참조](https://brunch.co.kr/@ourlove/68)



connect support completeConnection method

## SPRING서버로 JSON 보낼때 주의 사항(400Bad request발생시)
json형식으로 보내면 spring 서버에서 밑에와 받아야함
https://developers.facebook.com/apps/1827960967464787/settings/basic/

참고
* [Take Action](http://marobiana.tistory.com/58)
