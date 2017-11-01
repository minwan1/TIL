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

list<pojo> to json삽입
```java
List<TransferData> myList = dao.getList();
json.add("myList", gson.toJsonTree(myList));
```

이걸 위에와 같이 담으면 bankList에 담을 수 있다. fromJson은 JsonObject로부터 BankList클래스 형태로 변경하는것,


리스트 바로넣기
json.add("history", gson.toJsonTree(history));

## Circular reference

인터넷에서 찾오보니까 구글에서는 기본적으로 @exclude 방식을 지원안하는것같다. white list 방식(expose할 필드를 명시적으로 지정하는 방식)의 처리를 하는게 맞다고 본것일까...


```java
test2 = new ObjectMapper().writeValueAsString(user1);
test1 = new ObjectMapper().writeValueAsString(transferData);

@JsonManagedReference
@OneToMany(mappedBy="senderId", fetch = FetchType.EAGER)
private List<TransferData> transfers = new ArrayList<TransferData>();


@JsonBackReference
@ManyToOne
@JoinColumn(name = "sender_id")
private UserInformation senderId;
```




```
{
  "first_name": "wan",
  "last_name": "jin",
  "country": "KOR",
  "address_line1": "test",
  "city": "test",
  "postal_code": "123",
  "province": "test"
}
```
