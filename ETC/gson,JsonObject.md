## String to Json
```
JsonParser parser = new JsonParser();
JsonObject o = parser.parse("{\"a\": \"A\"}").getAsJsonObject();
```
## pojo to Json
```
jsonelement =gson.toJson(user.class,user);
```

## JsonObject에서 글빼오는방법
```
"{\"data\":[{\"cdKey << (test.get("test"))로 조회오면 이렇게됨
{"data":[{"cdKey" << (test.getAsString("test"))로 조회오면 이렇게됨
```
## JsonObject to pojo
```java
JsonObject temp = parser.parse(rslt.get("_result").getAsString()).getAsJsonObject();
bankList = (BankList) gson.fromJson(temp, BankList.class);
```
이걸 위에와 같이 담으면 bankList에 담을 수 있다. fromJson은 JsonObject로부터 BankList클래스 형태로 변경하는것,
