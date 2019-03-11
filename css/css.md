# Position
## static
기본값을 의미한다.

## relative

여기서 태그의 위치를 살짝 변경하고 싶을 때 position: relative를 사용합니다. 이제 top(위), right(오른쪽), bottom(아래), left(왼쪽) 속성을 사용해 위치 조절이 가능합니다.

## absolute
relative가 static인 상태를 기준으로 주어진 픽셀만큼 움직였다면, absolute는 position: static 속성을 가지고 있지 않은 부모를 기준으로 움직입니다. 만약 부모 중에 포지션이 relative, absolute, fixed인 태그가 없다면 가장 위의 태그(body)가 기준이 됩니다.

## Fixed 


참고
* [z](https://www.zerocho.com/category/CSS/post/5864f3b59f1dc000182d3ea1)