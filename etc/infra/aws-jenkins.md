## aws jenkins 설치

ssh -i "aws-wan.pem" 주소



```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
```


젠킨스 설치
```
sudo yum install jenkins
```

포트 변경
```
sudo vi /etc/sysconfig/jenkins
```

```
JENKINS_PORT="9090"
```


Jenkins 시작

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



java 설치
```
sudo yum install -y java-1.8.0-openjdk-devel.x86_64
```


inbound 포트 설정




http://ec2-54-180-30-249.ap-northeast-2.compute.amazonaws.com:9090/