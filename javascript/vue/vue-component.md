#  컴포넌트

컴포넌트는 Vue 오브젝트를 확장한 오브젝트이다. 여기서 확장이란 미리 Vue 오브젝트에 옵션을 정의해 이미 옵션이 정의된 Vue 오브젝트를 생성하는것을 의미한다. 컴포너트를 생성하면 확장된 Vue 인스턴스가 생성된다.




```html
<div id="app">
    <date></date>
    <date></date>
    <date></date>
</div>
```

```javascript

Vue.component('date',{
    template:'<div>{{ now }}</div>',
    data: function(){
        return {
            now new Date()
        }
    }
})

new Vue({
    el:'#app'
})
```


* 주의할점은 확장도니 Vue 인스턴스 즉 컴포넌트는 data를 정의할때 반드시 함수로 정의해야한다.

```html
<div id="app">
    <counter-component></counter-component>
    <counter-component></counter-component>
    <counter-component></counter-component>
</div>

```

```javascript
Vue.component('counter-componet',{
    template: '<button @click = counter += 1"> {{ counter }} </button>',
    data : {
    counter : 0
    }
})
```

위와 같이 컴포넌트를 사용하게 된다면 에러가 발생한다. 이러한 규칙은 Vue.js가 내부에서 처리하는 방법에 따른 규칙을 따른는것이 좋음.



```javascript
Vue.component('counter-componet',{
    template: '<button @click = counter += 1"> {{ counter }} </button>',
    data : function() {
        return { counter : 0}
    }
})
```

**제한사항**
* ul, ol, table, select같은 일부 에리먼트 내부에서는 컴포넌트를 사용할 수 없다.



## 데이터 전달
컴포넌트는 부모 자식끼리만 데이터를 전달 할 수 있다.

### 부모-자식 컴포넌트
Vue.js 에서는 부모 컴포넌트가 자식 컴포넌트에게 데이터를 전달하는것이 가능하다. 반면 자식컴포넌트는 불가능하다. 물론 event 발생으로 데이터를 전달할 수 있다.

* 부모 -> 자식 : props
* 자식 -> 부모 : emit

컴포넌트간에 데이터 교환은 필수다. 하지만 이렇게 되면 둘은 의존적이게된다.  하지만 컴포넌트간에 props, emit에 상호 약속으로만 데이터를 전달한다면 간접적인 의존성만 유지하게된다.

#### props
자식 컴포넌트가 부모 컴포넌트의 데이터를 참조하기 위해서는 props옵션을 사용해 부모 컴퍼넌트가 자식 컴포넌트에게 데이터를 전달할 수 있다.

```html
<div id="app">
    <simple-componet message="Hello, Vue!"></simple-component>
</div>
```
```javascript
new Vue({
    el: '#app',
    components : {
        'simple-component': {
            props: ['message'],
            template : '<div>{{ message }}</div>'
    }
})
```

**동적 props**

정적 props는 String 타입 외에 Number, Object, List 데이터는 전달할 수 없다. 또한 전달된 데이터는 변경 불가능하다. 만약 동적으로 데이터를 전달하려면 v-bind를 사용해야한다. 데이터가 상위에서 업데이트 될 때마다 하위 데이터로도 전달됩니다.

코드로 보면 아래와같을 수 있다.

```js
Vue.component('child', {
  // JavaScript는 camelCase
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```
```html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```
v-bind에 대한 단축 구문을 사용하는 것이 더 간단합니다.

```html
<child :my-message="parentMsg"></child>
```

아래는 좀 더 심화된 코드이다.
```html
<div id="app">
    <input type="text" v-model="message">
    <simple-componet :message="message"></simple-component>
</div>
```
```javascript
new Vue({
    el: '#app',
    components : {
        'simple-component': {
            props: ['message'],
            template : '<div>{{ message }}</div>'
    }
})

```

## 사용자 정의 이벤트

```html
<div id="app">
    <counter-component v-on:increment="incrementTotal"></counter-component>
    <counter-component v-on:increment="incrementTotal"></counter-component>
    <p>Total:{{ total }}</p>
</div>

```

```javascript
var counterComponent = {
    template:'<button @click="increment">{{ counter }} </button>',
    data: function(){
        return { counter : 0};
    },
    methods : {
        increment: function(){
            this.counter +=1;
            this.$emit('increment');
        }
    }

    new Vue({
        el:"#app",
        data: {
            total: 0
        },
        methods:{
            incrementTotal: function(){
                this.total += 1;
            }
        },
        components:{
            'counter-component':counterComponet
        }
    });

}
```


결과값
<button>1</button><button>2</button><br>
total:3


