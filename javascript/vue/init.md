## 인스턴스 사이클 훅

mounted
updated
destroyed


모든 라이프 사이클훅은 this 컨텍스트가 호출하는 vue 인스턴스를 가리키면 호출된다.







## computed와 watch
복잡한 로직이라면 반드시 computed 속성 을 사용해야 하는 이유입니다.


## 컴포넌트 작성
Vue.js에서 부모-자식 컴포넌트 관계는 props는 아래로, events 위로 라고 요약 할 수 있습니다. 부모는 props를 통해 자식에게 데이터를 전달하고 자식은 events를 통해 부모에게 메시지를 보냅니다. 어떻게 작동하는지 보겠습니다.

![](https://kr.vuejs.org/images/props-events.png)


