## Intellij로 효율적으로 개발하기(1) - Intellij Refactoring 기능
개발을 하다보면 Intellij의 리팩토링을 정말 많이 사용합니다. 가장 대표적인 예가 클래스나 변수명 등 네이밍 변경 같은 경우가 될 수 있습니다. 만약 프로젝트 내에 모든 클래스를 변경하는데 손수 하나하나 네이밍을 변경하게 된다면 엄청난 시간을 허비해야 할 것입니다.

하지만 Intelij에서 Rename과 같은 기능을 이용한다면 한 번에 효율적으로 모든 네이밍을 변경할 수 있습니다. 이러한 네이밍 변경과 같이 효율적인 리팩토링 기능이 어떤 것들이 있는지 알아보겠습니다.

키보드는 맥 기준으로 설명해드리겠습니다. 윈도우같은경우 스크린샷안에 내용을 참조해주시면 감사하겠습니다.


### Rename 기능
변수, 클래스, 메소드명을 한 번에 변경해야할 때 사용되는 기능입니다. 
단축키 : ⇧F6

![](https://media.giphy.com/media/2ZYF2T6oZhwRQSOc9U/giphy.gif)

Calculator2라는 클래스명을 변경했습니다. 변경 후 뜨는 창은 현재 Calclator2클래스를 사용하고 있는 변수들도 변경할지 물어보는 창입니다. 같이 선택을 하게 되면 사용되는 변수들도 함께 Calculator로 변경이 됩니다.


### 시그니처(파라미터) 변경 기능
이기능은 클래스또는 메소드에 관련된 있는 있는 시그니처(파라미터)를 동시에 변경하고자할 때 사용되는 기능입니다. 예를들어 오버라이딩된 클래스, implements한 클래스, 호출되어지는 메소드 등이 대상입니다. 

단축키 : ⌘F6

![](https://media.giphy.com/media/g4M2XpYWQzyvZIKpog/giphy.gif)

### 메소드 추출하기
이기능은 기존 메소드내에 기능이 2개라고 생각할 때 다른 메소드로 분리해내는 기능입니다.
단축키 : ⌥⌘M
![](https://media.giphy.com/media/cI43RnYZYe4F3Crg5s/giphy.gif)
다음과 같이 계산하는 메소드가 있다고 했을때 여기에서 더하기 기능을 메소드로 빼내고싶다면 위와같이 추출해낼 수 있습니다.


### inline 기능 사용하기
이기능은 두개의 라인이 하나의 라인으로 합쳐져도 무방할 때 사용하는 기능입니다. 예를들어 다음과같이 Calculator클래스의 add라는 함수를 사용했을 때 c라는 결과값이 나오게됩니다. 그리고 c를 return하여 할수를 종료합니다. 하지만 여기에서 바로 add(a,b)를 리턴할 수 있습니다. 이렇게 하나의 라인으로 합치려고할때 사용할 수 있습니다.

단축키 : ⌥⌘N
![](https://media.giphy.com/media/8FGM8CmvLj1DzGgkUl/giphy.gif)

### 변수 추출하기
다음 기능은 변수가 선언되지 않은 연산을 변수로 추출해낼 떄 사용하는 기능입니다. 
단축키 : ⌥⌘V
![](https://media.giphy.com/media/3MfkF5HSKKDXtKJnkD/giphy.gif)

### 필드 변수로 추출하기
다음기능은 로컬변수를 필드변수로 뽑아낼 때 사용하는 기능입니다.
단축키 : ⌥⌘F
![](https://media.giphy.com/media/3FlLs9iLtklaDrUdD2/giphy.gif)

### 파라미터 추출하기
다음은 메소드나, 생성자에 있는 파라미터에 필드를 추가하고싶을 때 사용 하는 기능이다. 

단축키 : ⌥⌘P
![](https://media.giphy.com/media/1gTQTENa4OtKhwBnEz/giphy.gif)


### 클래스 추출하기
다음은 하나의 클래스의 기능이 너무커지거나 메소드의 기능이 너무 커질때 해당 기능을 클래스로 분리해내는 기능입니다. 
단축키 : ⌃T -> 9.Delegate
![](https://media.giphy.com/media/xUb9f91b5mCZlptTzd/giphy.gif)


### 사용되지 않는 내용 삭제하기
다음은 사용되지 않는 변수, 클래스, 메소드등 삭제할 때 해당 기능을 안전하게 제거하는 기능입니다. 그림과 같이 사용되지 않는 곳에가서 ⌥enter 클릭하게되면 Intellij에서 safe-delete를 제안해줍니다. Minus가 어디에서 호출되지는 않지만 Minus와 관련된 변수, 메소드가있는경우 스샷과 같이 Intellij에서 관련된것들도 같이 삭제할건지 물어보게됩니다. 이과정을 검토한후 삭제를 하게되면 안전하게 소스를 지우실 수 있습니다. 이기능을 이용하면 사용되지 않는 소스들을 깔끔하게 지우실 수 있습니다.
단축키 : ⌥enter -> safe-delete
![](https://media.giphy.com/media/21S3x4GAh4XeHZDqZq/giphy.gif)


### ⌃T, ⌥enter 사용하기
리팩토링 하려고 하는 대상으로 가셔서 ⌃T, ⌥enter 둘중 하나를 클릭하시면 좀더 많은 기능들의 intellij의 리팩토링을 이용하 실 수 있습니다. ⌃T는 입력하시면 Intellij에서 미리 목록화 해놓은 리팩토링 기능의 목록을 보실 수 있습니다. ⌥enter 같은 경우에는 Intellij에서 해당 소스를 파악하고 제안해주는 리팩토링 기능을 확인하실 수 있습니다. 물론 intellij에서 refactoring할 대상을 찾지 못한다면 제안 되는 리팩토링기능은 없을것입니다.



참고
* [Yun-blog](https://cheese10yun.github.io/intellij-refactoring/)
* [Jetbrain](https://www.jetbrains.com/)