# 함수 정의와 호출
이장에서는 함수정의및 호출예제를 실행해볼것이다. 또 추가로 확정함수와 프로퍼티를 사용해 자바라이버릐를 코틀린 스타일로 적용하는 방법을 살편본다.


## 코틀린에서 컬렉션만들기
셋 선언하기

```
val set = hashSetOf(1, 7);
val list = arrayListOf(1, 7, 53)
val map = hashMapoF(1 to "one", 7 to "seven")
```
코틀린은 자체적인 컬렉션이 아닌 자바의 컬렉션클래스를이용한다. 위와같이 선언해도 실상 자바컬렉션을 사용하는것이다. 


## 함수를 호출하기 쉽게 만들기
자바 컬렉션에는 디폴트 toString 구현이 들어있다. 하지만 그 디폴트 toString의 출력 형식은 고정돼 있고 우리에게 필요한 형식이 아닐 수 도 있다.
```kotlin
val list = listOf(1, 2, 3)
println(list) // [1, 2, 3] - toString() 호출
```
디폴트 구현과달리 (1; 2; 3)처럼 원소 사이를 세미콜론으로 구분하고 괄호로 리스트를 둘러싸고싶다면 어떻게 해야할까? 이를 위해서는 자바프로젝트에서 구아바나 아파치 커먼즈를 이용해서 처리를해야한다. 하지만 코틀린에서는 이러한 기능을 표준라이브러리 내에서 제공해준다.

```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
): String{
    val result = StringBuilder(prefix)
    for((index, element) in collection.withIndex()){
        if(index > 0){
            result.append(separator)
        }else{
            result.append(element)
        }

        result.append(postfix)
        retrun result.toString()
    }
}

val list = listOf(1, 2, 3)
println(joinToString(list, "; ", "(", ")"))
// (1; 2; 3)
```
위와같이 하면 우리가 원한 결가값을 얻을 수 있다. 하지만 이함수를 좀더 간단하게 하고싶다.

### 이름 붙인 인자
해결하고픈 첫 번쨰 문제는 가독성이다. joinToString 함수를 살펴보자
```kotlin
joinToString(list, "; ", "(", ")")
```
자바에서는 위에서 이렇게  함수로 호출하게되면 호출은되겠지만 정확히 어떤 시그니처들이있는지 이해하기도 힘들며 가독성또한 떨어진다. 하지만 코틀린에서는 파라미터 인자 이름을 지정하여 이러한 문제를 해결할 수 있다
```kotlin
joinToString(collection, separator = " ", prefix =" ", postfix = ".")
```

코틀린으로 작성한 함수를 호출할 때는 함수에 전달하는 인자 중 일부의 이름을 명시할 수 있다. 호출시 인자중 어느하나라도 이름을 명시하고 나면 혼동을 막기 위해 그뒤에 오는 모든 인자는 이름을 꼭명시해야한다.


### 디폴트 파라미터값
자바에서는 일부 클래스에서 오버로딩한 메소드가 너무 많아진다는 문제가 있다. 하지만 이러한 애매한 문제를 코틀에서는 디폴트 파타리터값을 통해 문제를 해결 했다. 다음은 디폴트 파라미터를 지정한 예제이다.

```kotlin
fun<T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String


>>>joinToString(list, ", ", "", "");
1, 2, 3
>>>joinToString(list);
1, 2, 3
>>>joinToString(list, "; ");
1; 2; 3

```

함수의 디폴트 파라미터 값은 함수를 호출하는 쪽이 아니라 함수 선언 쪽에서 지정된다.

### 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티
자바에서 특정 클래스에 종속되지 않는 메소드들을 보통 util클래스에 뺴놓는다. 하지만 코틀린에서는 이러할 필요가 없다. 코틀린은 함수 자체를 import 할 수 있다. 대신 함수를 소스파일의 최상위에 작성하고 그 패키지를 임폴하면된다.
```kotlin
package strings
fun joinToString(...): String {...}
```


### 메소드를 다른 클래스에 추가: 확장함수와 확장 프로퍼티
개념적으로 확장함수는 단순하다. 확장함수는 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그클래스의 밖에 선언된 함수다.  확장함수를 보여주기 위해 어떤 문자열의 마지막 문자를 돌려주는 메소드를 추가해보자.

```kotlin
package strings
fun String.lastChar(): Char = this.get(this.length - 1)
```
확장 함수를 만들려면 추가하려는 함수 이름앞에 그 함수가 확장할 클래스의 이름을 덧붙이기만 하면 된다. 이 함수를 호출하는 구문은 다른 일반 클래스 멤버를 호출하는 구문과 똑같다.

## 문자열과 정규식 다루기