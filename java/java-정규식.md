# 정규식 / 정규표현식 (Regular Expressions; Regex)

## 대표적인 정규 표현식

표현식  | 설명
------- | ------- 
^ | 문자열의 시작
$ | 문자열의 종료
. | "임의의 한 문자 (문자의 종류 가리지 않음)단, \ 는 넣을 수 없음"
* | 앞 문자가 없을 수도 무한정 많을 수도 있음
+ | 앞 문자가 하나 이상
? | 앞 문자가 없거나 하나있음
[] | 문자의 집합이나 범위를 나타내며 두 문자 사이는 - 기호로 범위를 나타낸다. []내에서 ^가 선행하여 존재하면 not 을 나타낸다.
{} | 횟수 또는 범위를 나타낸다.
() | 소괄호 안의 문자를 하나의 문자로 인식
| | 패턴 안에서 or 연산을 수행할 때 사용
\s | 공백 문자
\S | 공백 문자가 아닌 나머지 문자
\w | 알파벳이나 숫자
\W | 알파벳이나 숫자를 제외한 문자
\d | 숫자 [0-9]와 동일
\D | 숫자를 제외한 모든 문자
\ | "정규표현식 역슬래시(\)는 확장 문자 <br>역슬래시 다음에 일반 문자가 오면 특수문자로 취급하고 역슬래시 다음에 특수문자가 오면 그 문자 자체를 의미"
(?i) | 앞 부분에 (?i) 라는 옵션을 넣어주면 대소문자를 구분하지 않음



## 예제 1 
예제의 `^[0-9]*$` 를 분석해보면 

>^ 으로 우선 패턴의 시작을 알립니다.
>[0-9] 괄호사이에 두 숫자를 넣어 범위를 지정해줄 수 있습니다.
>\* 를 넣으면 글자 수를 상관하지 않고 검사합니다.
>$ 으로 패턴의 종료를 알립니다.



## 예제 2.
1. 숫자로 시작하는것 검색
>\d+(숫자로시작하는것)

2. 문장안에서 날짜값 빼오기

>2018/7/3 테스트행 크크크를 손에 넣었다.
>자동차를 탄다 18년06월13일 날에
>\d+.\d+.\d+ -> 2018/7/3 , 18년06월13 잡힌다.




## 자바 예제

1. 
```java

        Pattern pattern = Pattern.compile("\d+.\d+.\d+");
        final Matcher matcher = pattern.matcher("2018/7/3 테스트행 크크크를 손에 넣었다. 2018/7/4");

        if(matcher.find()){ //find 안쓰고 바로 group뽑으려고하면 Exception남.
            final String group = matcher.group();// 2018/7/3 만 추출되어짐
        }

        final String group = matcher.group();// 2018/7/3 
        matcher.find();
        final String group = matcher.group();// 2018/7/4
```


2. replaceAll (패턴에 맞는 값을 새로운 값으로 치환)

​
```java
public void replaceRegEx() {
    String target = "나는 2008년도에 입학했다.";
    String regEx = "[0-9]";
    Pattern pat = Pattern.compile(regEx);
  
    Matcher m = pat.matcher(target);
    String result = m.replaceAll("2"); // 패턴과 일치할 경우 "2"로 변경
     
    System.out.println("출력 : " + result);
    // 출력 : 나는 2222년도에 입학했다.
}
```




참고
* [HighCode](http://highcode.tistory.com/6)
* [자바 정규식 기본정리](http://blog.naver.com/PostView.nhn?blogId=bb_&logNo=220863282423&parentCategoryNo=&categoryNo=103&viewDate=&isShowPopularPosts=true&from=search)
