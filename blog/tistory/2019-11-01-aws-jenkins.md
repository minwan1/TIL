# AWS EC2 jenkins 설치

<!-- TOC -->

- [AWS EC2 jenkins 설치](#aws-ec2-jenkins-%EC%84%A4%EC%B9%98)
    - [CI(Continuous Integration)란](#cicontinuous-integration%EB%9E%80)
    - [CD(Continuous Delivery or Continuous Deploy) 란?](#cdcontinuous-delivery-or-continuous-deploy-%EB%9E%80)
    - [젠킨스 설치](#%EC%A0%A0%ED%82%A8%EC%8A%A4-%EC%84%A4%EC%B9%98)
    - [젠킨스(어드민화면) 포털 설정](#%EC%A0%A0%ED%82%A8%EC%8A%A4%EC%96%B4%EB%93%9C%EB%AF%BC%ED%99%94%EB%A9%B4-%ED%8F%AC%ED%84%B8-%EC%84%A4%EC%A0%95)
    - [젠킨스 실행, 종료 명령어](#%EC%A0%A0%ED%82%A8%EC%8A%A4-%EC%8B%A4%ED%96%89-%EC%A2%85%EB%A3%8C-%EB%AA%85%EB%A0%B9%EC%96%B4)

<!-- /TOC -->

## CI(Continuous Integration)란
개발자가 각각 개발한 소스코드를 모아 컴파일, 테스트, 빌드 등을 하는 과정을 통합하여 해주는 프로세스를 말한다. 보통 이러한 프로세스를 각각 별개로 시행하게 되면 많은 시간과 자원이 낭비되기 때문에 이러한 프로세스를 통합해서 해준다. 이러한 통합을 CI라고 한다. 보통 이러한 CI 서버로는 Jenkins, Travis 등이 있다

## CD(Continuous Delivery or Continuous Deploy) 란?
CD는 팀이 소프트웨어를 짧은 주기로 생산하는 소프트웨어 엔지니어링 방식으로, 소프트웨어를 릴리스할 때 언제든지 소프트웨어를 안정적으로 릴리스할 수 있도록 합니다. 보다 빠른 속도와 빈도로 소프트웨어를 구축, 테스트 및 릴리스하는 것을 목표로 합니다. 이 접근 방식을 사용하면 프로덕션 환경의 응용 프로그램을 많은 업데이트할 수 있으므로 변경 내용을 전달할 비용, 시간 및 위험을 줄일 수 있습니다. 간단하고 반복 가능한 배포 프로세스가 CD에서 중요하다.

여기에서는 엔터프라이즈급에 회사에 많이 사용하는 jenkins를 Yum 패키지 설치자를 이용해서 설치해볼 것입니다. 다른 리눅스 환경도 설치하는 방법은 크게 다르지 않을 것입니다.



## 젠킨스 설치
먼저 리눅스 서버에 접속하여 아래의 명령어를 실행해줍니다.

```
sudo yum update
```
그냥 바로 yum을 이용해 jenkins 서버를 설치하려고 하면 아래와 같이 메시지가 나오면서 패키지가 없다고 설치할 수 없습니다.
![](https://i.imgur.com/SXcPlzD.png)

그렇기 때문에 아래와같이 먼저 jenkins 패키지를 설치합니다. 
```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
```

그런다음 jenkins 저장소에 키를 설치합니다.
```
sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
```


그다음 이제 수동으로 저장소에 jenkins 패키지를 설치했으니 yum install로 젠킨스를 설치합니다.
```
sudo yum install jenkins
```
![](https://i.imgur.com/ZliPhfH.png)
그러면 위와 같이 정상적으로 젠킨스가 설치되는 것을 볼 수 있습니다. 그런 다음 기본적으로 포트가 8080으로 되어있는데 이것을 그냥 사용하셔도 되지만 변경해서 사용할 수 있습니다. 만약에 하나에 서버에서 톰캣과 젠킨스를 사용하신다면 변경해서 사용하는 것이 좋습니다. 먼저 아래와 같이 jenkins 파일을 엽니
```
sudo vi /etc/sysconfig/jenkins
```
그런다음 아래의 부분을 찾아 포트를 8080에서 9090으로 변경해줍니다.
![](https://i.imgur.com/9VkYj5u.png)
```
JENKINS_PORT="9090"
```
그다음 젠킨스를 실행하면 됩니다. 실행명령어는 아래와 같습니다.

```
sudo service jenkins start
```
만약 아래와 같은 에러가 에러 발생한다면 java 8로 변경하셔야합니다. 자바 8설치방법은 [이글](https://wan-blog.tistory.com/61) 을 보시면됩니다
![](https://i.imgur.com/lP1lMkF.png)

그다음 다시 젠킨스를 실행시킵니다. 그다음에  아래와같이 젠킨스 포탈에 접속하시면 됩니다. **물론 포트는 그대로 유지하되 도메인주소는 변경해주셔야합니다**
```
http://ec2-59-190-45-249.ap-northeast-2.compute.amazonaws.com:9090/
```
그런데 AWS 같은경우 접속이 안될 수 있습니다. 그렇기 때문에 AWS 홈페이지에 들어가서 보안그룹을 열어줘야합니다.
![](https://i.imgur.com/yfmkUu0.png)
위 그림에서는 포트 8080과 9090으로 열어줫습니다. **사용자 지정같은경우 일단 간단한 테스트를 위해 자신의 IP를 열어줍니다.**

## 젠킨스(어드민화면) 포털 설정

![](https://i.imgur.com/V7MVA3e.png)
그런다음 젠킨스 포털에 접속합니다. 그럼 아래와같은 화면이 뜨는데요. 이때 스크린샷에서 설명해주는것과 같이 `sudo vi /var/lib/jenkins/secrets/initialAdminPassword`에 파일을 열어 초기비밀번호를 받아 입력해줍니다.

```java
sudo vi /var/lib/jenkins/secrets/initialAdminPassword
```
그런다음에 어드민 유저를 생성해줍니다.
![](https://i.imgur.com/jImLWOg.png)

이제 어드민 유저를 생성하고 다음화면을 누르면 jenkins화면이 뜬것을 확인할 수 있습니다. 이렇게 간단하게 젠킨스를 설치해봤습니다.
![](https://i.imgur.com/4Xs0Os8.png)


## 젠킨스 실행, 종료 명령어
젠킨스 실행
```
sudo service jenkins start
```

Jenkins 종료
```
sudo service jenkins stop
```

Jenkins 재시작

```
sudo service jenkins restart
```
