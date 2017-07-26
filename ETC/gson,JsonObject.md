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

list<pojo> to string
```java
List<FacebookPage> facebookPage
arrayString = gson.toJson(facebookPage);
```

이걸 위에와 같이 담으면 bankList에 담을 수 있다. fromJson은 JsonObject로부터 BankList클래스 형태로 변경하는것,


```

## json object 로거 출력










```
public BankList getKoreaBankList() throws Exception {

		BankList bankList = null;

		Gson gson = new Gson();

		CtiHandler hanlder = new CtiHandler(payGateTiService);
		JsonObject header = new JsonObject();
		JsonObject input = new JsonObject();
		header.addProperty(CommonCode.METHOD_KEY, "GET");
		JsonObject body = new JsonObject();
//		body.addProperty("langCd", language); for language

		input.addProperty(CommonCode.BODY_KEY, body.toString());

		JsonObject rslt = hanlder.execute("paygate/endpoint/query_bank_list", header, input);

		JsonObject temp = rslt.get("_result").getAsJsonObject();



//		RestTemplate rest1= new RestTemplate();
//		ResponseEntity<String> result= rest1.getForEntity("https://stg5.paygate.net/v5/code/listOf/banks?_method=GET",String.class);
//		JsonParser parser = new JsonParser();
//		JsonObject temp = parser.parse(result.getBody()).getAsJsonObject();
//		bankList = (BankList) gson.fromJson(temp, BankList.class);


		bankList = (BankList) gson.fromJson(temp, BankList.class);

		return bankList;
	}

```
for (CtiFilter filter : filters) {
  filter.postHandler(target, input, json);
}
