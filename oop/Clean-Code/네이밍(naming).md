# Interface 네이밍

Interface
reader, notifier


# 함수 네이밍 

## 한가지만해라
함수는 한가지 기능만해야한다.

## 함수당 추상화 수준은 하나로
한함수내에 추상화 수준을 섞기 시작하면, 깨어진 창문처럼 사람들이 함수에 세부 사항을 점점 더 추가한다. 

함수가 확실히 '한가지'작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야한다. 

함수는 간단한 To 문단으로 추상화시켜 사용할 수 있다. 
```java
public String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite){
    if(isTestPage(pageData))
        includeSetupAndTeardownPages(pageData, isSuite);
    
    return pageData.getHtml();

}
```
ToRenderPageWithSetupsAndTeardowns, 설치페이지와 해제페이지와 함께 렌더하기 위한 함수.

지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그함수는 한가지 작업만 한다.

## 함수 내 섹션

예를들어 어떤 함수에 아래와같은 기능들이 있다고해보자.
```
String html = pageData.getHtml(); // 고수준 추상화
String pagePathName = PathParser.render(pagepath); //중수준 추상화
String result = temp.append("\n"); // 저수준 추상화

```
위에 한 함수내에 추상화 수준이 섞어진 코드이다. 특정 표현이 근본 개념인지 아니면 세부사항인지 구분하기 어려운 탓이다. 이렇게 근본사항과 세부사항이 섞이기 시작하면, 깨어진 창문처럼 사람들이 함수에 세부사항을 점점 더 추가한다.

