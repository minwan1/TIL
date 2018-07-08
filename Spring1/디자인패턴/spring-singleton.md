 singleton pattern은 인스턴스가 사용될 때에 똑같은 인스턴스를 만들어 내는 것이 아니라, 동일 인스턴스를 사용하게끔 하는 것이 기본 전략이다(인스턴스가 하나만 만들어지는 전역변수와 같은 것을 말합니다.)

 spring 기본적으로 왜 싱글톤으로 생성이 될까를 생각했었음. 스프링이 주로 적용되는 대상이 대부분 자바 엔터프라이즈 기술을 사용하는 서버 환경이기 때문입니다.

```
public class Singleton {
	private static Singleton uniqueInstance;

	private Singleton(){}

	public static Singleton getInstance(){
		if (uniqueInstance == null){
			uniqueInstance = new Singleton();
		}
		return uniqueInstance;
	}
 }

```


출처
[정리정리정리](http://jusungpark.tistory.com/16)
