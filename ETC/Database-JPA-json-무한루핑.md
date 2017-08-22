## 무한 루핑 이론(Circular reference)

hibernate 양방향 설정을하게된 pojo model을 string으로 바뀌려면 양방향으로 객체를 바라보고있어서 무한루핑이 빠지게된다. 그문제를 해결하기위해 여러가지 방법이있다. (아마 지연로딩에서 이문제가 발생하는 것 같다.)

```java
test2 = new ObjectMapper().writeValueAsString(user1);
test1 = new ObjectMapper().writeValueAsString(transferData);


//user
@JsonManagedReference
@OneToMany(mappedBy="senderId", fetch = FetchType.EAGER) //상대쪽 변수이름으로
private List<TransferData> transfers = new ArrayList<TransferData>();

//transfer
@JsonBackReference
@ManyToOne
@JoinColumn(name = "sender_id") //참조레퍼스 옵션넣으면 그냥 username넣으면될듯
private UserInformation senderId;
```
