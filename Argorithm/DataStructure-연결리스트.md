## Abstract Data Type(추상 자료형 ADT의 이해)
구체적인 기능의 완성과정을 언급하지 않고, 순수하게 기능이 무엇인지를 나열한것

예)지갑
- 카드사입
- 카드의 추출
- 동전의 삽입
- 동전의 추출
- 지폐의 삽입
- 지폐의 추출


## 리스트의 이해
순차리스트 - 배열을 기반으로 구현된 리스트
연결 리스트 - 메모리의 동적 할당을 기반으로 구현된 리스트


```c
void void ListInit(List * plist);
void LInsert(List * plist, LData data);
int LFirst(List * plist, LData * pdata);
int LNext(List * plist,LData * pdata);
LData Lremove(List * plist);
int LCount(List * plist);
```

연결리스트 참조일련의 과정
LFirst -> LNext -> LNext


연결리스트의 개념적이해 1 볼차례
