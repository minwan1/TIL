# Kotlin 기반에 Spring Boot Multi module 구성 하기

<!-- TOC -->

- [Kotlin 기반에 Spring Boot Multi module 구성 하기](#kotlin-%EA%B8%B0%EB%B0%98%EC%97%90-spring-boot-multi-module-%EA%B5%AC%EC%84%B1-%ED%95%98%EA%B8%B0)
    - [프로젝트 생성](#%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1)
    - [Sub 프로젝트 생성](#sub-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1)
    - [프로젝트 구조](#%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B5%AC%EC%A1%B0)
    - [Gradle 셋팅](#gradle-%EC%85%8B%ED%8C%85)

<!-- /TOC -->

이 프로젝트 예제는 Kotlin 기반에 Spring boot 멀티 모듈 프로젝트를 구성합니다. **cingration라는 루트 프로젝트**를 구성하고 그 아래 api, core(도메인)에 서브 프로젝트를 구성할 것입니다. 또한 이프로젝트에서는 빌드 스크립트로는 Kotlin dsl을 사용할 것입니다.

* core : 도메인 프로젝트
* api : api프로젝트


## 프로젝트 생성
(1) New Project > Gradle > Additional Libraries and Frameworks > Kotlin 선택 > Next
![](https://i.imgur.com/PTBI2lN.png)

(2) GroupId, ArtifactId 입력 > Next
![](https://i.imgur.com/jaCtJSa.png)
(3) Use auto-import 체크 해제되어있는지 확인 > Next > Finish

* 프로젝트 생성시 가끔씪 gradlew가 생성이 안되는데 이건 intellij가 가끔씩 제대로 인식이 안되서 그런것같습니다.

## Sub 프로젝트 생성

여기에서는 도메인(core)과 API에대한 두개의 프로젝트를 추가할것 입니다.
(1) File > New > Module
![](https://i.imgur.com/qlxYIcV.png)
(2) Gradle > Additional Libraries and Frameworks > Kotlin 선택 > Next
(3) Group, Version Inherit 체크 되어있는지 확인 (선택이 안되어있다면 전에 만들었던 루트프로젝트를 클릭 com.wan.multi)
![](https://i.imgur.com/RLphGAe.png)
(4) ArtifactId 입력 > Next
(5) Sub Project는 Root Project 바로 아래에 모듈이 생성되어야 함. Content root 경로 잘 확인하기 > Finish

마찬가지로 core프로젝트도 성성해줍니다.


## 프로젝트 구조

![](https://i.imgur.com/1yrPjFb.png)


## Gradle 셋팅

그다음은 gradle설정을 해줘야한다. 위에서 말한대로 Kotlin dsl을 이용해서 프로젝트들에 gradle을 셋팅하겠습니다.

**settings.gradle**
```kotlin
include("api", "core")
```


**root build.gradle**
```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    base
    kotlin("jvm") version "1.3.50" apply false
}

allprojects {
    group = "com.wan.boot"
    version = "1.0-SNAPSHOT"
    repositories {
        mavenCentral()
    }
}

dependencies {
    subprojects.forEach{
        archives(it)
    }
}
```

sub core(doamin) build.gradle
```kotlin

import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    kotlin("jvm")
}

dependencies {
    implementation(kotlin("stdlib-jdk8"))
}

tasks.withType<KotlinCompile> {
    kotlinOptions.jvmTarget = "1.8"
}
```
sub api build.gradle

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    kotlin("jvm")
    kotlin("plugin.spring") version "1.3.50"
    id("org.springframework.boot") version "2.2.0.RELEASE"
    id("io.spring.dependency-management") version "1.0.8.RELEASE"
}

dependencies {

    implementation(project(":core"))
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin")
    implementation("org.jetbrains.kotlin:kotlin-reflect")
    implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8")
    testImplementation("org.springframework.boot:spring-boot-starter-test") {
        exclude(group = "org.junit.vintage", module = "junit-vintage-engine")
    }
}
tasks.withType<KotlinCompile> {
    kotlinOptions.jvmTarget = "1.8"
}
```



모든 소스코드는 [깃허브](https://github.com/minwan1/blog-example/tree/master/kotlin-multi-module)를 이용해 참고하실 수 있습니다.
