# Quasar 프레임워크 설치

## Quasar란
이번에 프로젝트하면서 팀원분중에서 quasar라는 프레임워크를 도입하게되어 알게된 프로젝트인데 정말 편리합니다. Quasar는 vue cli를 한번 Wrapping한 프레임워크입니다. Quasar를 이용하면 빠르게 반응형 웹/앱을 만들 수 있습니다. 다음은  Qusar가 가지는 이점입니다.

* SPAs (Single Page App)
* SSR (Server-side Rendered App) (+ optional PWA client takeover)
* PWAs (Progressive Web App)
* Mobile Apps (Android, iOS, …) through Cordova or Capacitor
* Multi-platform Desktop Apps (using Electron)
    * 명령어 하나로 멀티 플랫폼에 앱을 만들 수 있습니다.

Quasar 모터는 하나의 코드를 작성하고 모바일앱, 일렉토랜앱 웹사이트 등에 배포하자는 모토입니다. Quasar는 그리고 경량한 프레임워크입니다. **따로 Hammerjs, Momentjs or Bootstrap에 의존을 추가할 필요가 없습니다.**

이것은 이미 수백개가 넘는 component들이 정의되어 있어서 쉽게 화면을 구 성할 수 있습니다. 아마 빠르게 서비스를 만들거나 어드민화면을 만들때는 정말 좋은 프레임워크같습니다.

다음은 이미 Quasar에 정의된 목록입니다.
* https://quasar.dev/vue-components/ajax-bar


## Quasar 설치하기

먼저 Quasar 노드 모듈을 설치합니다
```
npm install -g @quasar/cli
```


그런다음 quasar 프로젝트를 생성합니다. 
```
quasar create today-life-ui

```

그러면 다음과 같은 화면이 나오는데요. vue cli와 비슷합니다. 프로젝트이름, 제품이름, 제품설명, 프로젝트 생성자, css 스타일, Quasar components 자동 import여부, Quasar에서 사용할 부가적 라이브러리 의존성 추가, eslint 사용여부, cordova등을 이용해 앱설치 필요한 패키지 이름 설정, npm 설치도구순으로 물어보고 이설정을 완료하고 설치를 시작합니다.
![](https://i.imgur.com/F5NWwa2.png)

설치가완료되면 다음과같이 해당 폴도로 이동합니다.
```
cd today-life-ui
```
그런다음 추가로 노드 모듈들을 설치합니다.
```
yarn
```

그런다음 아래의 명령어로 앱을 실행시킵니다.

```
quasar dev
```

그런다음 브라우저에 다음 주소를 입력합니다.
```
http://localhost:8080/#/
```
그렇게되면 아래와같이 심플하게 실행된 Quasar SPA웹을 볼 수 있습니다.
![](https://i.imgur.com/UFcEFvV.png)