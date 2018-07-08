## Java란
자바는 썬 마이크로 시스템즈에서 개발하여 1996년 1월 공식적으로 발표한 객체지향언어이다.

기본적으로 자바프로그램들은 JVM위에서 동작한다. JVM 위에서 동작하기 위해서는 자바 바이트코드가 필요하다. 이 자바 바이트코드는 개발자에 의해서 작성된 자바 소스코드(.java)를 자바컴파일러(javac)로 변환 된 코드(.class)를 말한다. 아래 간단하게 자바프로그램 특징을 정리해보았다.

### 자바 프로그램 특징
1. 자바프로그램은 운영체제에 독립적이다.
2. 객체지향 언어이기 때문에 유지보수가 용이하다.
3. 자동으로 메모리관리를 해준다.
4. 동적 로딩을 지원한다.
5. 오픈소스 자료가 풍부하다.
6. 자바프로그램은 안정적이다.

## JVM이란
JVM이란 JAVA Virtual Machine, 자바 가상 머신의 약자를 따서 주려 부르는 용어이다. JVM 역할은 자바 애플리케이션을 클래스 로더를 통해 읽어 들여 자바 API와 함께 실행하는 것이다. 그리고 JVM은 JAVA와 OS사이에서 중개자 역할을 수행하여 운영체제에 독립적인 플랫폼을 갖게 해준다.

또한 JVM은 프로그램의 메모리 관리를 알아서 해준다. C프로그램 같은 경우 에는 직접 메모리할당을 해주고 해지해줘야한다. 하지만 JAVA에서는 JVM이 자동으로 Memory 관리를 해주는 장점이 있다.

### JVM 실행구조
일반적인 프로그램은 Windows또는 Linux같은 운영체제 위에서 실행된다.
![](https://i.imgur.com/zV6xu0h.png)
<center>그림1</center>

하지만 위에서 설명한대로 자바프로그램 같은경우에는 위 오른쪽 그림과 같이 운영체제위에 자바 가상 머신위에서 실행이된다. 이렇게 JVM위에서 굳이 자바 프로그램을 돌리는 이유는 자바 프로그램을 운영체제에 상관없이 실행시키기 위함이다.
예를들어 윈도우에서 동작하도록 구현된 워드 프로그램은 Linux에서 동작하지 않는다. 이 워드 프로그램을 Mac환경에서 돌리기 위해서는 Mac기반으로 다시 구현해야 한다. 이렇게 운영체제가 다르면 같은 프로그래밍의 언어로 구현한다 할지라도 운영체제에서 일부 다른 기능이 존재하기 때문이다. 예를 들어 그래픽, 키보드 마우스 관련 기능들은 운영체제에 의해서 제공되기 때문에, 운영체제가 달라지면 동일한 프로그래밍 언어일지라도 기능의 완성법이 달라진다. 하지만 자바 프로그램 같은 경우에는 아래의 그림과 같이 운영체제에 맞는 JVM을 다운로드한다면 어떤 환경에 운영체제에서도 자바 프로그램을 실행 실킬 수 있다.


### JVM 실행과정
1. 프로그램이 실행되면 JVM은 OS로부터 이 프로그램이 필요로 하는 메모리를 할당 받는다. JVM은 이 메모리를 용도에 따라 여러 영역으로 나누어 관리한다.
2. 자바 컴파일러(javac)가 자바소스(.java)코드를 읽어 들여 자바 바이트코드(.class)로 변환시킨다.
3. 이 변경된 Class 파일들을 Class Loader를 통해 JVM 메모리영역(Runtime Data Areas) 영역으로 로딩한다.
4. 로딩된 class파일들은 Execution engine을 통해 해석된다.
5. 해석된 바이트코드는 Runtime Data Areas에 배치되어 실질적인 수행이 이루어지게된다.
이러한 실행과정속에서 JVM은 필요에 따라 Thread Synchronization과 GC같은 관리 작업을 수행한다.

아래는 JVM 실행과정을 그림으로 표현한것이다.

![](https://i.imgur.com/Vy1JC1b.png)
<center>그림2</center>


JVM 내에 기능들을 좀 더 세부적으로 알아보자.
#### Java Compiler
자바 소스(.java)코드를 Byte code(.class)로 변환하는 역할을 한다.

#### Class Loader
JVM은 컴파일 타임이 아닌 런타임시에 처음으로 클래스를 참조할때 해당 클래스를 로드하고 RuntimeDataArea에 배치한다. 이 동적 로드를 담당하는 부분이 JVM의 Class Loader이다.

#### Runtime Data Areas
런타임 데이터 영역은 JVM이라는 프로그램이 운영체제 위에서 실행되면서 할당받는 메모리 영역이다. 런타임 데이터 영역은 5개의 영역으로 나눌 수 있다. 이중 PC 레지스터(PC Register), JVM 스택(JVM Stack), 네이티브 메서드 스택(Native Method Stack)은 스레드마다 하나씩 생성되며 힙(Heap), 메서드 영역(Method Area) 은 모든 스레드가 공유해서 사용한다.
<center>

 ![](https://i.imgur.com/zv717Hf.png)

</center>
<center>그림 3</center>

아래그림은 실제 런타임시에 어떻게 클래스들과 객체 그리고 지역변수들이 할당되는지에 대한 것을 그림으로 표현한것이다.

![](https://i.imgur.com/pAh5gIZ.png)
<center>그림 4</center>

##### PC Register
레지스터는 각 스레드마다 하나씩 존재하며 스레드가 시작될 때 생성된다. PC레지스터는 Thread가 어떤 명령어로 실행되어야 할지에 대한 기록을 하는 부분으로 현재 수행중인 JVM명령의 주소를 갖는다.

##### Stack Area
이곳에는 지역변수, 매개변수, 메소드 정보, 연산중 발생하는 임시 데이터등 저장이 된다.JVM은 오직 JVM 스택에 스택 프레임을 추가하고(push) 제거하는(pop) 동작만 수행한다. 예외 발생 시 printStackTrace() 등의 메서드로 보여주는 Stack Trace의 각 라인은 하나의 스택 프레임을 표현한다.

> Stack에 저장되는 Element의 단위는 frame 이라한다.

##### Native method stack area
자바 프로그램이 컴파일되어 생성되는 바이트코드가 아닌 실제 실행할 수 있는 기계어로 작성된 프로그램을 실행시키는 영역이다. JAVA가 아닌 다른 언어로 작성된 코드를 위한 공간이다.
JNI(Java Native Interface)를 통해 호출하는 C/C++등의 코드를 수행하기위한 스택 공간이다.

##### Heap Area
Runtime 에 동적으로 할당되는 데이터가 저장되는 영역입니다. 예를들어 객체생, 배열등이 생성되었을 때 저장되는 공간이다. Heap에 할당된 데이터는 GC의 대상입니다. JVM 성능 등의 이슈에서 가장 많이 언급되는 공간이다.

##### Method Area(Class Area, Code Area, Static Area)
메서드 영역은 모든 스레드가 공유하는 영역으로 JVM이 시작될 때 생성된다. JVM이 읽어 들인 각각의 클래스와 인터페이스에 대한 런타임 상수 풀, 필드와 메서드 코드, Static 변수, 메서드의 바이트코드 등을 보관한다.



###### Runtime constant pool
Runtime constant pool 은 Method area 내부에 존재하는 영역으로, 각 클래스와 인터페이스의 상수뿐만 아니라, 메서드와 필드에 대한 모든 레퍼런스까지 담고 있는 테이블이다. 즉, 어떤 메서드나 필드를 참조할 때 JVM은 런타임 상수 풀을 통해 해당 메서드나 필드의 실제 메모리상 주소를 찾아서 참조하여 중복을 막는 역할을 한다.



#### Execution Engine
Load된 Class의 ByteCode를 실행하는 Runtime Module이 바로 Execution Engine이다. Class Loader를 통해 JVM 내의 Runtime Data Areas 에 배치된 바이트 코드는 Execution Engine에 의해 실행되며, 실행 엔진은 자바 바이트 코드를 기계어로 변경한 뒤에 사용하게 된다. 해당 작업을 실행 엔진이 하게 된다.. 그리고 바이트 코드를 기계어로 변경할 때엔 두 종류의 방식을 사용한다. 각각 Interpreter와 JIT (Just-In-Time) compiler이다.

Interpreter는 명령어를 그때그때 해석해서 실행하게 되어 있다. JIT (Just-In-Time) compiler는 인터프리터의 단점(성능,속도 등)을 보완하기 위해 도입되었다. 실행 엔진이 인터프리터를 이용해 명령어를 하나씩 실행하지만 JIT 컴파일러는 적정한 시간에 전체 바이트 코드를 기계어로 변경한다.


#### Native Method Interface(JNI)
Native Method Interface는 줄여서 JNI라고도 한다. JNI는 JVM에 의해 실행되는 코드 중 네이티브로 실행하는 것이 있다면 해당 네이티브 코드를 호출하거나 호출 될 수 있도록 만든 일종의 프레임워크입니다

#### Native Method Libraries
네이티브 메소드 실행에 필요한 라이브러리들을 뜻한다.


## Garbage Collection
GC는 Java Application에서 사용하지 않는 메모리를 자동으로 수거하는 기능을 말한다. 예전의 전통적인 언어 C등의 경우 malloc, free등을 이용해서 메모리를 할당하고, 일일이 그 메모리를 수거해 줘야 했다. 그러나 Java 언어에서는 GC 기술을 사용함에 따라서 개발자로 하여금 메모리 관리에서 부터 좀더 자유롭게 해주었다.

GC의 실행순서이다.
* 참조되지 않은 객체들을 탐색 후 삭제
* 삭제된 객체의 메모리를 반환
* Heap 메모리의 재사용


## 마무리하며
자바가 메모리를 효율적으로 관리하기위해 자바 가상머신을 알아야한다. 동일한 기능의 프로그램이더라도 메모리 관리에 따라 성능이 좌우된다고한다. 메모리 관리가 되지 않으면 속도저하현상등이 일어날 수 있다.


참고
* [훈마로의 보물창고](http://hoonmaro.tistory.com/9)
* [Jbee](http://asfirstalways.tistory.com/158)
* [Lazysoul](https://medium.com/@lazysoul/jvm-%EC%9D%B4%EB%9E%80-c142b01571f2)
* [jdm](http://jdm.kr/blog/188)
* [D2](https://d2.naver.com/helloworld/1230)
* [이것이 자바다](http://book.naver.com/bookdb/book_detail.nhn?bid=8589375)
