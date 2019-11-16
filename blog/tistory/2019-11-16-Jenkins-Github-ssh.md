# Jenkins 에 Github ssh 연동하기

젠킨스에 깃허브에 소스를 가져와 빌드 하는 방법은 여러 가지이지만 여기에서 ssh 기반으로 github 소스를 가져와 빌드 하는 방법으로 Github를 연동할 것이다. 먼저 jenkins가 설치가 안 되어있다면 이 글을 참조하면 좋다. [젠킨스 설치](https://wan-blog.tistory.com/65)

Jenkins github SSH 연동 크게 3가지 순서로 이루어집니다

- 젠킨스 서버에서 키생성
- 깃허브 Public key 등록
- 젠킨스 Private key 등록

위와 3개의 구성을 하게 되면 Jenkins에서 github 소스를 가져와 빌드를 할 수 있다.

## 젠킨스 서버에서 키생성

먼저 젠킨스를 실행해야 합니다. 그리고 jenkins 유저 권한으로 SSH keygen을 실행해야 하기 때문에 jenkins 권한으로 접속한다.

    sudo -u jenkins /bin/bash

그런 다음 젠킨스에 SSH 환경을 위해 public키와 private 키를 생성 해준다.

    ssh-keygen -t rsa

위와 같이 실행하게 되면 다음과 같이 어느 위치에 키들을 생성할 것인지 암호 설정을 할 것인지가 나온다. 따로 설정하지 않고 엔터를 입력해 키들을 생성한다.

![](https://i.imgur.com/cPivAtt.png)

그런 다음 SSH키들이 생성된 곳으로 이동합니다.

    cd /var/lib/jenkins/.ssh

## 깃허브 Public key 등록

그런 다음 해당 public 키와 private 키를 각각 github와 젠킨스에 등록해야 한다. 먼저 Github public key를 등록하도록 하겠습니다. 아래와 같이 github 사이트에 들어가 deploy key 등록을 합니다.

![](https://i.imgur.com/1BiAuoE.png)

그런다음 아래에 경로를 public key를 복사하여 위에 key 입력창에 입력합니다.

    cat /var/lib/jenkins/.ssh/id_rsa.pub

그런다음 다음과 같이 키가 등록된 모습을 볼 수 있습니다.

![](https://i.imgur.com/lqNXshT.png)

## Jenkins Private key 등록

그런 다음 Jenkins에 private 키를 등록 해줘야 합니다.  Jenkins → System → Global → Add Credentials로 이동합니다.

![](https://i.imgur.com/DacEjDS.png)

그러면 다음과 같은 폼이 뜨는데 정보를 입력합니다.

![](https://i.imgur.com/DhXFBbf.png)

먼저 종류를 SSH로 입력하고 차례대로 입력합니다.

- Username
    - 젠킨스에서 보여줄 별칭입닌다
- private key
    - cat /var/lib/jenkins/.ssh/id_rsa 복사하여 첨부(**private 키부분만 복사하는게아니라 전체 복사하는거 추천**)

## 빌드 프로젝트 생성

jenkins → new item → 프로젝트 생성(Freestyle project) 에서 이제 git 설정을 합니다.

![](https://i.imgur.com/nCBaUgC.png)

Git hub연결이 정상적으로 처리 되는 것을 볼 수 있습니다.