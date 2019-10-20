## 코틀린
코틀린은 자바 플랫폼에서 돌아가는 새로운 프로그래밍 언어다.코틀린은 간결하고 실용적이며, 자바 코드와 상호 운용성을 중시한다. 

### 코틀린 맛보기 예제
다음 예제는 Person이라는 클래스를 정의하고 그클래스를 사용해 사람을 모아둔 컬렉션을 만들고, 가장 나이가 많은 사람을 찾는 예제이다. 코틀린 예제는 http://try.kotl.in 에 접속하면 이 예제를 쉽게 실행해 볼 수 있다.

```kotlin
data class Person(val name: String,
                 val age: Int? = null) // 널이 될 수 있는 타입(Int)과 파라미터 디폴트 값

fun main(args: Array<String>) { // 최상위 함수
    
    val persons = listOf(Person("영희"), Person("철수", age = 29)) // 이름 붙은 파라미터
    val oldest = persons.maxBy { it.age ?: 0} // 람다 식과 엘비스 연산자
    
    
    println("나이가 가장 많은 사람: $oldest") // 문자열 템플릿
}
```
설명하면 다음과 같다.
* name age라는 프러퍼티가 들어간 간단한 class이다.
* age프로퍼티의 디폴트값은 null이다.
* 영희의 나이를 지정하지 않았기 때문에 null이 사용된다.
* 리스트에서 가장 나이가 많은 사람을 찾기 위해 maxBy 함수를 사용한다.
* maxBy 함수에 전달한 람다 식은 파라미터를 하다 받는다.
* it라는 이름을 사용하면(별도로 파라미터 이름을 정의하지 않아도)람다식의 유일한 인자를 사용할 수 있다. 엘비스 연산자라고도 한다.)
* ?: 는 age가 null인경우 0을 반환하고 그렇지 않은 경우 age의 값을 반환한다.

간단한 예제를 통해 코틀린을 스터디해봤다.




## 코틀린 기초

### 함수와 변수

코틀린은 아래와같이 fun이라는 키워드를 이용해 함수를 지정할 수 있다. 그리고 파라미터 뒤에 타입을 지정한다.

```kotlin
fun max(a: Int, b: Int): Int{
    return if(a > b) a else b
}

println(max(1, 2));
```
자바에서 변수를 선언할 때 타입이 맨 앞에 온다. 코틀린에서는 타입 지정을 생략하는 경우가 흔하다. 타입으로 변수 선언을 시작하면 타입을 생략할 경우 식과 변수선언을 구별할 수 없다.

```kotlin
val question = "삶, 우주, 그리고 모든것에 대한 궁극적인 질문"
var answer = 42
//위에서는 타입표기를 생략했지마 ㄴ원한다면 타입을 명시해도 된다.
val answer: int = 42
```

만약 변수의 타입을 지정하지않으면 컴파일시 할당되어진값을 기반으로 코틀린이 판단하여 타입을 지정한다. 
참고로 코틀린에서 변수 선언은 키워드는 아래와같이 두개다
val = value의미로 값을 변경 가능함.
var = variable의미로 값을 변경 가능함.



### 클래스와 프로퍼티

**클래스**

코틀린에서 간단한게 Person class를 선언해보자

```
class Person(val name: String)
```

**프로퍼티**
그다음은 프로퍼티를 봐보자.

```kotlin
class Person(
    val name: String, //읽기전용으로 단순 getter만 제공한다.
    var isMarried:Boolean //getter,setter를 제공한다.
)
```

**커스텀 접근자**
이번에는 프로퍼티의 접근자를 직접 작성하는 방법을 확인해보자. 직사각형 클래스인 Rectangle을 정의하면서 자신이 정사각형인지 알려주는 기능을 만들어보자. 

```kotlin
class Rectangle(val height: Int, val width: Int){
    val isSquare: Boolean
    get () {
        return height == width
    }
}

var rectangle = Rectangle(41, 43)
println(rectangle.isSquare) // false
```

isSquare프로퍼티에는 자체값을 저장하는 필드가 필요없다. 이 프로퍼티에는 자체 구현을 제공하는 게터만 존재한다. 클라이언트가 프로퍼티에 접근할 때마다 게터가 프로퍼티값을 매번 다시 계산한다.

### 선택 표현과 처리: enum과 when
when은 자바의 switch를 대치하되 훨씬 더 강력하다.
**enum class 선언**
```
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```
**다음은 프로퍼티와 메소가 있는 enum 클래스 선언하기 예제이다.**
```kotlin
enum class Color (val r: Int, val g: Int, val b: Int){
    RED(255, 0, 0), 
    ORANGE(255, 165, 0), 
    YELLOW(255, 255, 0), 
    GREEN(0, 255, 0), 
    BLUE(0, 0, 255), 
    INDIGO(75, 0, 130), 
    VIOLET(238, 130, 238)

    fun rgb() = (r * 256 + g) * 256 + b
}
```
**when을 사용해 올바른 enum 값 찾기**
```kotlin
fun getMnmonic(color :Color) = 
    when(color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }
```
자바와 달리 각 분기 끝에 break를 넣지 않아도 된다. 만약 한분기에 여러값을 넣고 싶다면 ,를 이용할 수 있다.

```kotlin
fun getMnmonic(color :Color) = 
    when(color) {
        Color.RED -> "Richard"
        Color.ORANGE, Color.YELLOW -> "Of"
    }
}
```

### 코틀린의 예외 처리
코틀린의 예외처리는 다음과같은 구문으로 처리 가능하다.
```kotlin
throw IllegalArgumentException("exception");
```

