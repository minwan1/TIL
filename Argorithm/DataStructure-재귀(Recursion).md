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


스프링에서 쓰레드들을 관리한다.
씽글쓰레드 멀티쓰르데 멤버변수만 조심하자 ? 는뭐지

view로 갈려면 그냥 준영속상태로 만드는것도 방법일듯.


```c
#include <stdio.h>

int basearch(int arr[],int left,int right,int target){

    int mid;
    mid =  (left + right)/2 ;
    if(left > right){
        return -1;
    }

    if(arr[mid] == target){
        return mid;
    }else if(target<arr[mid]){
        return basearch(arr,left,mid-1,target);
    }else{
       return basearch(arr,mid+1,right,target);
    }
}

int main(int argc, const char * argv[]) {
    // insert code here...
    printf("Hello, World!\n");
    int ar[] = {1,3,5,7,9,10,13,16,18,25,33,34};
    int test = sizeof(ar)/sizeof(int)-1;
    printf("%d zxcv",test);
    int s = basearch(ar,0, sizeof(ar)/sizeof(int)-1,18);
    if(s == -1){
        printf("인자없음\n");
    }else{
        printf("%d <<이게인자\n",s);
    }
    return 0;
}


```

## 하노이타워
![](https://i.imgur.com/ErlzgLs.png)
목적1. 원반 4개를 A에서 C로 이동
- 큰원반 n개를 A에서 C로 이동
- HanoiTowerMove(num,from,by,to)
목적2. 원반 3개를 A에서 B로 이동
- 원반 n-1개를 A에서 B로 이동
- HanoiTowerMove(num-1 ,from,to,by)
목적3. 큰원반한개를 A에서 C로 이동
- 큰원반 1개를 A에서 C로 이동
 - printf(...);
목적4. 작원원반 3개를 B에서  C로이동  
- 작은원반 n-1개를 B에서 C로 이동
- HanoiTowerMove(num-1,by,from,to)
