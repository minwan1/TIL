## Java Reflection이란
리플렉션이란 객체를 통해 클래스의 정보를 분석해 내는 프로그램 기법을 말한다.

reflection 은 자바의 특징이다. 실행중인 자바프로그램 내부를 검사하고 내부의 속성을 수정할 수 있도록 한다. 예를 들어, 어떤 자바클래스가 가진 모든 멤버의 이름을 얻거나 보여줄 수 있다.


아래는 객체에 멤버변수값을 출력하는 소스이다.

```java

            StringBuilder sb = new StringBuilder();

            Class<?> thisClass = null;
            thisClass = Class.forName(obj.getClass().getName());

            Field[] aClassFields = thisClass.getDeclaredFields();
            sb.append(obj.getClass().getSimpleName() + " [ ");
            for (Field f : aClassFields) {
                f.setAccessible(true);
                String fName = f.getName();
                sb.append("(" + f.getType() + ") " + fName + " = " + f.get(obj) + ", ");
            }
            sb.append("]");


```


출처: http://gyrfalcon.tistory.com/entry/Java-Reflection [Minsub's Blog]


