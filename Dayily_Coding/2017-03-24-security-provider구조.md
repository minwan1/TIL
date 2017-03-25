# Daily Coding 2017-03-21일 spring security 암호화

## AuthenticationProvider
* 맨위에 AuthenticationProvider클래스가 존재
* 그아래 AbstractUserDetailsAuthenticationProvider class존재
* sprng에서 구현되어져있는 DaoAuthenticationProvider class가 있음.


authenticate? 이걸구현한다.


사용자 정의 예외 생성
```java
public class MyException extends Exception{
    public MyException(){
        super("내가 만든 예외");
    }
}

public class ExceptionTest {
    static void callException() throws MyException{
        throw new MyException();
    }
    public static void main(String args[]){
        try{
               callException();
              }catch(MyException e){
                      System.out.println(e.getMessage());
               }catch(Exception e){
                       System.out.println(e.getMessage());
               }finally{
                     System.out.println("시스템 종료.");
              }    
    }
}

```

참고
* [개발이 하고 싶어요](http://hyeonstorage.tistory.com/203)
