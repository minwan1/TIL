# Gradle


## <groupId>
프로젝트를 생성한 조직 또는 그룹명으로 보통, URL의 역순으로 지정한다. groupid는 프로젝트마다 접근할 수 있는 고유한 이름을 만들도록 한다. 보통은 패키지 네이밍룰(도메인 뒤집기)을 따르는게 정석이다. 이후에 원하는 만큼 하위 그룹을 만들 수있다. 



## <artifactId>
프로젝트에서 생성되는 기본 아티팩트의 고유 이름이다. artifactid 는 jar 파일에서 버젼 정보를 뺀 이름이다. 자유롭게 적되, 소문자를 사용하고 이상한 특수문자를 사용하지 않는다. 



## <version>
애플리케이션의 버전. 접미사로 SNAPSHOT이 붙으면 아직 개발단계라는 의미이며, 메이븐에서 라이브러리를 관리하는 방식이 다르다고 한다.

## <packaging> : 
jar, war, ear, p/om등 패키지 유형을 나타낸다.

## <name>
프로젝트 명

## <description>
프로젝트 설명

## <url>
프로젝트를 찾을 수 있는 URL




|repositories|주요 내용|
|-|-|
|repositories|저장소 설정|
|dependencies | 의존 관계 설정 | 
| buildscript | 빌드 스크립트 클래스 패스 설정 |
| initscript | 초기화 스크립트 설정 |
| configugrations | 환경 구성 설정 |
| allprojects | 서브 프로젝트 포함 전체 프로젝트 설정 |
| sybprojects | 서브 프로젝트 설정 |
| artifacts | 빌드 결과에 대한 설정 |



출처: https://jeong-pro.tistory.com/168 [기본기를 쌓는 정아마추어 코딩블로그]

출처: https://seotory.tistory.com/32 [seotory]









## 그레이들의 스크립트파일

### ext를 이용한 변수선언

```gradle
ext{
    extPro1 = 'pro1'
    extPro2 = 'pro2'
}

println '속성값 1 : ' + ext.extPro1
println '속성값 2 : ' + ext.extPro2
```


### gradle.properties를 이용한 변수 관리

gradle.properties
```properties
msg=Hi, Gradle!
```

build.gradle
```
task hello<<{
    println msg
}
```

```
$ gradle hello
```



## 그레이들 태스크
태스크는 하나의 작업단위이다

```
task hello<<{
    println msg
}
```