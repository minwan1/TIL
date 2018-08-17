# IO 패키지 소개
프로그램에서는 데이터를 외부에서 읽고 다시 외부로 출력하는 작업이 빈번히 일어난다. 데이터는 사용자로부터 키보드를 통해 입력될 수 있고, 파일 또는 네트워크로부터 입력될 수 있다. 또 반대로 모니터나 네트워크등으로 출력 될 수 있다.

>자바에서 스트림(Stream)을 통해 입출력되므로 스트림의 특징을 잘 이해해해야한다. 스트림은 단일 방향으로 연속적으로 흘러가는것을 말하는데, 물이 높은곳에서 낮은 곳으로 흐르듯이 데이터는 출발지에서 나와 도착지로 들어간다는 개념이다.
# 입력 스트림과 출력 스트림

프로그램이 출발지냐 또는 도착지냐에 따라서 스트림의 종류가 결정되는데, 프로그램이 데이터를 입력받을 때에는 입력 스트림(InputStream)이라 부르고, 프로그램이 데이터를 보낼 때에는 출력 스트림(OutputStream)이라고 부른다. 
![스크린샷 2018-08-12 오전 3.01.03](/assets/스크린샷%202018-08-12%20오전%203.01.03.png)

항상 프로그램을 기준으로 데이터가 들어오면 입력스트림이고, 데이터가 나가면 출력 스트림이라는것을 명심해야한다.

자바의 기본적인 데이터 입출력(IO : Input/Output) API는 java.io 패키지에서 제공한다. java.io 패키지에는 파일 시스템의 정보를 얻기 위한 File 클래스와 데이터를 입출력하기 위한 다양한 입출력 스트림 클래스를 제공한다.

|java.io 패키지의 주요 클래스|설명|
|--|--|
|File | 파일 시스템의 파일 정보를 얻기 위한 클래스  |
|Console | 콘솔로부터 문자를 입출력하기 위한 클래스 |
|InputStream / OutputStream |바이트 단위 입출력을 위한 최상위 입출력 스트림 클래스 |
|DataInputStream / DataOutputStream< br> ObjectInputStream / ObjectOutputStream <br> PrintStream <br> BufferedInputStream / BufferedOutputStream | 바이트 단위 입출력을 위한 하위 스트림 클래스 |
| Reader / Writer | 문자 단위 입출력을 위한 최상위 스트림 클래스 |
| FileReader / FileWriter <br> InputStreamReader / OutputStreamWriter <br> PrinterWriter <br> BufferedReader / BufferedWriter | 문자 단위 입출력을 위한 하위 스트림 클래스 |

스트림 클래스는 크게 두분류의 타입인 바이트기반 스트림과/ 문자기반의 스트림으로 구분된다.
**바이트기반 스트림**
그림, 멀티미디어, 문자 등 모든 종류의 데이터를 받고 보낼 수 있다
**문자 기반 스트림**
문자 기반 스트림은 오로지 문자만 받고 보낼 수 있도록 특화되어 있다.

바이트 기반 스트림과 문자 기반 스트림은 최상위 클래스에서 다음과 같의 구분된다.

![스크린샷 2018-08-12 오전 3.35.55](/assets/스크린샷%202018-08-12%20오전%203.35.55.png)


InputStream은 바이트 기반 입력 스트림의 최상위 클래스이고, OutputStream은 바이트기반 출력 스트림의 최상위 클래스이다.



* ByteArrayStream 공부하기

## InputStream


# 콘솔 입출력

# 파일 입출력
# 보조 스트림








## 알고리즘 


```
1 2 3 4 5 6 7 8 9 10 11 12 // 한줄 입력

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

String[] s = br.readLine().split(" ");



// s[0] = "1"; Integer.parseInt(s[0]) => 1

// s[1] = "2";

// s[2] = "3";

// .....

```

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in);

StringTokenizer st = new StringTokenizer(br.readLine());
// AB CDD EFFF GH 입력

st.nextToken() // AB

st.nextToken() // CDD

st.nextToken() // EFFF

st.nextToken() // GH

```

속도적인 부분에서 StringTokenizer가 조금 더 빠르다.



```java
String str = "ab cd  ef";

StringTokenizer st = new StringTokenizer(str, " ");
for (int i = 0; st.hasMoreTokens(); i++) System.out.println("#" + i + ": " + st.nextToken());

String[] split = str.split(" ");
for (int i = 0; i < split.length; i++) System.out.println("#" + i + ": " + split[i]);


//StringTokenizer
#0: ab
#1: cd
#2: ef
//String.split()
#0: ab
#1: cd
#2: 
#3: ef

```

출처: http://mygumi.tistory.com/78 [마이구미의 HelloWorld]