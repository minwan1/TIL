SFTP(Secure File transfer protocol) - SSH와 마찬가지로 전송시 암호화시켜서 전송을 하게 되는데 그 중에 FTP와 같이 파일을 전송할 때 암호화 시켜서 전송합니다.



ftp 서버

* [mac에서 ftp서버 구축하기](https://devist.tistory.com/42)


# Spring 에서 SFTP로 파일 업로드하기
이예제는 Spring integration 모듈을이용해 로컬에서 원격 SFTP서버에 업로딩하는 예제이다.


요약
* DefaultSftpSessionFactory 생성
* SftpMessageHandler 생성
* uploadGateway

# SftpConfig 구성
먼저 SFTP SessionFactory를 구성해야만 합니다. session factory는 기본적으로 SFTP에 대한 정보를 적는다.(계정, 패스워드)

그다음 우리는 Messagehandler를 구성해야만 합니다. 여기에서는 Sftp를 연동할것이기 때문에 SftpMessageHandler class를 이용할 것 입니다. 이 클래스에 역할은 원격 SFTP에 파일을 얻로드하는 역할입니다.


gateWay




## sftp 설치

inetutils