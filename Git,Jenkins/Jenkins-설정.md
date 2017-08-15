## Jenkins

```
빌드는 CPU와 메모리, 디스크를 많이 소비하는 작업이다.
지속적인 통합 서버는 동시에 여러 개의 빌드를 수행하고 결과물을 갖고 있어야 하므로 이슈 관리나 버전 관리, 아티팩트 관리보다 더 많은 하드웨어 자원이 필요하다.
별도의 서버가 있다면 젠킨스 전용으로 분리시키고 그럴 여유가 없다면 젠킨스를 구동하는 WAS 에 메모리와 디스크를 여유있게 할당하도록 하자
```

핫디플로이 : 톰켓 재가동없이 소스변화를 반영하는것
프로비저닝 : 특정서비스를 제공받기 위하여 서비스 실행부터 시작해 서비스를 제공받기전단계까지 처리되는 일련의 절차를 말한다.

CI(Continuous Integration)란
개발자가 각각 개발한 소스코드를 모아 통합하여 빌드하는 과정을 특정시점이 아닌 주기적으로 새행함으로써 통합에서 발생하는 오류를 사전에 해결하고 이러한 과정들에 소용되는 시간을 줄이기 위함이다. 배포를 위한 빌드단계,테스팅단계등에서 시간을 절야 할 수 있다.


## CI시스템을 구축하기 위한 핵심 구성요소
### CI 서버
* Jenkins
### SCM
* Git
### Build Tool(컴파일,테스트,정적분석등 동작가능한 소프트웨어로 생성하는 도구)
* maven

빌드 스크립트를 통한 CI 자동화 수행절차
1. 소스코드를 바이너리 파일로 컴파일한다.
2. 바이너리 파일을 배포형태로 패키징한다.
3. 패키징한 파일을 테스트서버에 배포한다.

## jenkins 설치하기
1. 젠킨스.war 다운
    * http://mirrors.jenkins.io/war-stable/latest/jenkins.war
2. 포트열어주기
    * aws의 경우 홈페이지에서 열어주고, 일반리눅스의경우 명령어로 포트열기
3. 톰켓 실행
4. jenkins관리에서 Configure global security의 회원관리 설정
5. Global tools Configuration
    * 메이븐 플러그인 설치(http://www.nextree.co.kr/p2129/)
    * java(jdk)  /usr/java/jdk1.8.0_05/
    * git (version control tool)
    * Maven (build tool)
6. publish over ssh 플러그인추가
    * jenkins관리 - 시스템설정 - publish over SSH
    * ssh 서버설정



14.63.213.32.8080





참고
* [조대협의 블로그]( http://bcho.tistory.com/654)
* [Devlog](http://asfirstalways.tistory.com/303)


젠킨스 삭제
http://stackoverflow.com/questions/38604715/how-can-i-remove-jenkins-completely-from-linux

http://35.165.30.71:9090/jenkins
