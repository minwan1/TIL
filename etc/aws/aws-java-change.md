## AWS EC2(Amazon Linux)에 Java8 설치하기
EC2는 기본적으로 JAVA 1.7을 사용한다. 그렇기 때문에 젠킨스나 그 위에 자바 버전을 사용하려면 리눅스에 설치된 자바 버전을 변경해줘야 합니다.

먼저 현재 버전을 확인하면 자바 1.7버전인 것을 알 수 있습니다.
```
java -version
```
자바 버전을 바꾸기 위해서는 먼저 openjdk8을 설치하셔야 합니다.

```
sudo yum install -y java-1.8.0-openjdk-devel.x86_64
```
설치를 완료한 다음에 기존에 설치된 7버전에 8버전으로 변경해주셔야 합니다. 아래의 명령어를 통해 설치된 버전을 변경합니다.

```
sudo /usr/sbin/alternatives --config java
```
위 명령어를 치시면 아래의 스크린샷과 같이 선택된 버전을 선택하라는데 `2`를 선택하시면 됩니다.
![](https://i.imgur.com/knE0CDc.png)

그다음은 기존에 설치된 버전을 제거합니다.
```
sudo yum remove java-1.7.0-openjdk
```

그리고 현재 버전을 다시 확인하시면 1.8로 변경된 모습을 볼 수 있습니다.

![](https://i.imgur.com/95mxG01.png)

간단하게 AWS EC2에 자바버전 7을 8로 변경하는 방법을 알아봤습니다.