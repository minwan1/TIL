## 재귀(Recursion)

재귀함수
```c

void Recursive(void){
  printf("Recursive call!");
  Recursive();
}
```
CPU에 위에 함수를 복사해서 여러개의 함수를 cpu에 올려놓고 차례대로 실행된다.
![](http://i.imgur.com/bZY5HoM.png)
원본을 차례대로 복사해서 cpu에 올려놓는다.

```c
void Recursive(int num)
{
  if(num <= 0)
    return ;

  printf("Recursive call")
  Recursive(num-1);
}
int main(){
  Recursive(3);
  return 0;
}
```
함수를 호출하면 스택에 그함수들이 쌓인다. 지역변수나 전역변수를 선언하지 않더라도 돌아갈 주소값들을 메모리에저장해야하기때문에 메모리에 할당된다.

재귀활용2볼차례
