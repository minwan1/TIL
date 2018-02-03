일단 exception으로 다던지라
마지막에 모든 exception받아서 처리


```java
public abstract class SocialException extends RuntimeException {

	public SocialException(String message) {
		super(message);
	}

	public SocialException(String message, Throwable cause) {
		super(message, cause);
	}
}
```

```java
public class ApiException extends SocialException {

	private String providerId;

	public ApiException(String providerId, String message) {
		this(providerId, message, null);
	}

	public ApiException(String providerId, String message, Throwable cause) {
		super(message, cause);
		this.providerId = providerId;
	}

	public String getProviderId() {
		return providerId;
	}

}

```
```java
public class NotAuthorizedException extends ApiException {

	public NotAuthorizedException(String providerId, String message) {
		super(providerId, message);
	}

}

```
```java
public class MissingAuthorizationException extends NotAuthorizedException {

	public MissingAuthorizationException(String providerId) {
		super(providerId, "Authorization is required for the operation, but the API binding was created without authorization.");
	}

}

```







throw new HttpServerException(e.getMessage(), e);
caused by로 계속 추적할수있음 위와같이 저장하면.




public void test123(){
  String test = null;
  int a = test.length();
}


test123(); 호출<< 하면

아래와같이 에러찍힘(따로 throws 설정을 안해줘도)
Caused by: java.lang.NullPointerException: null
	at com.sodatransfer.web.transfer.TransferService.test123(TransferService.java:951)
	at com.sodatransfer.web.transfer.TransferService.createTransfer(TransferService.java:922)
	... 140 common frames omitted





	if (!(rslt.get(CommonCode.STRING_RESULT).getAsInt() == 200)) {


		if (!(rslt.get(CommonCode.RESULT_KEY).getAsJsonObject().get("status").getAsString().equals("SUCCESS"))) {
	logger.error(rslt.toString());
	throw new HttpServerException("don`t create seyfert`memeber");
}


@Transactional(rollbackFor={Exception.class},noRollbackFor={HttpNoRollbackException.class})


@Transactional(rollbackFor={Exception.class},noRollbackFor={HttpNoRollbackException.class})


Caused by: javax.persistence.RollbackException: Transaction marked as rollbackOnly
	at org.hibernate.jpa.internal.TransactionImpl.commit(TransactionImpl.java:58)
	at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:517)
	... 138 common frames omitted


	Caused by: org.springframework.transaction.TransactionSystemException: Could not commit JPA transaction; nested exception is javax.persistence.RollbackException: Transaction marked as rollbackOnly



sms

## sms
### 실패했을때
{
  "messages": [
    {
      "to": "8280249080",
      "status": {
        "groupId": 5,
        "groupName": "REJECTED",
        "id": 8,
        "name": "REJECTED_PREFIX_MISSING",
        "description": "Number prefix missing"
      },
      "smsCount": 1,
      "messageId": "c2e6962d-aa3f-4902-80e7-7411b7f1ac0b"
    }
  ]
}


### 성공했을때
{
  "messages": [
    {
      "to": "821080249080",
      "status": {
        "groupId": 1,
        "groupName": "PENDING",
        "id": 7,
        "name": "PENDING_ENROUTE",
        "description": "Message sent to next instance"
      },
      "smsCount": 1,
      "messageId": "ea328238-3a61-4c2d-8091-a4708f000ce2"
    }
  ]
}

한글 39자 가능
