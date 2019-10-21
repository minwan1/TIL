# Spring 멀티 모듈 구성

## Root 프로젝트 생성

(1) New Project > Gradle > Additional Libraries and Frameworks > Next
![](https://i.imgur.com/RPE6oGa.png)
(2) GroupId, ArtifactId 입력 > Next
(3) Use auto-import 체크 해제되어있는지 확인 > Next > Finish


**GroupId란?**
GroupId는 어떤 한회사에서 식별될 수 있는 값으로 설정하는게 좋다. 예를들어 wan라는 회사가 첫 프로젝트 이름으로 com.wan.boot라는 이름으로 groupid를 가져갔다고 해보자. 그다음 규모가 켜저서 payment를 분리하는 프로젝트를 만들었다고해보자 그렇게되면 그 해당 프로젝트에 group id는 com.wan.payment가 될것이다. 보통은 도메인에 해당하는게 그룹 id가 된다.

**ArtifactId란?**
jar파일의 버전을 제외한 즉 프로젝트 이름라고 생각하면 된다. 예를들어 multi-sample라는 프로젝트를 만들 수 있을것이다.

## Sub 프로젝트 생성
여기에서는 도메인과 API에대한 두개의 프로젝트를 추가할것이다.
(1) File > New > Module
(2) Gradle > Additional Libraries and Frameworks > java 선택 > Next
(3) Group, Version Inherit 체크 되어있는지 확인 (선택이 안되어있다면 전에 만들었던 루트프로젝트를 클릭 com.wan.multi)
(4) ArtifactId 입력 > Next
(5) Sub Project는 Root Project 바로 아래에 모듈이 생성되어야 함. Content root 경로 잘 확인하기 > Finish


## 프로젝트 구조
2개의 서브 모듈 프로젝트를 추가하면 다음과 같은구조로 만들어졌을 것이다.
![](https://i.imgur.com/35lMCEM.png)



## Gradle 셋팅
그다음은 gradle설정을 해줘야한다. 보통은 도메인(core) 프로젝트를 만들고 그 도메인을 공유하여 여러개의 프로젝트를 생성하는 형식으로 사용한다. 예를들어 api프로젝트와 batch 프로젝트를 따로 만들었다고해도 도메인 프로젝트를 공유해서 사용할 수 있을것이다.


**settings.gradle**

```groovy
rootProject.name = 'multi-sample'

include 'multi-sample-core'
include 'multi-sample-api'
```

**root build.gradle**

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '2.2.0.RELEASE'
    id 'io.spring.dependency-management' version '1.0.8.RELEASE'
}

repositories {
    mavenCentral()
}

```

**sub api build.gradle**
```groovy
plugins {
    id 'java'
    id 'org.springframework.boot'
    id 'io.spring.dependency-management'
}

group 'com.wan.multi'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    implementation project(':multi-sample-core')

    implementation 'org.springframework.boot:spring-boot-starter-web'

    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'

    testCompile group: 'junit', name: 'junit', version: '4.12'
}


```

**sub core(doamin) build.gradle**
```groovy
plugins {
    id 'java'
    id 'org.springframework.boot'
    id 'io.spring.dependency-management'
}

group 'com.wan.multi'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'

    testCompile group: 'junit', name: 'junit', version: '4.12'
}

```

## 마무리
멀티 프로젝트를 구성하는 이유는 도메인, 공통 레파지토리을 공유해서 사용하기 위한것이다. 예를들어 같은 도메인을 각각 배치, API 프로젝트 공유하지않고 각각 중복해서 만들었다고해보자. 이럴경우 도메인에 필드가 추가되거나 없어진다고하면 각각에 프로젝트를 다수정해야하는데 이런식으로 멀티프로젝트를 구성을하게되면 관리 포인트가 하나로 줄어들기 때문에 유지보수성이 좋아진다.



## 참고
* [Sungjun.Dev](https://gwonsungjun.github.io/articles/2019-04/gradle_multi_module)