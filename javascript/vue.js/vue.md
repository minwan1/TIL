# Vue 인스턴스란?
Class : 붕어빵 틀
Object : 붕어빵
Instance : 각각의 붕어빵



Vue 인스턴스란 생성된 Vue 오브젝트 하나를 의미한다. Vue 인스턴스는 Vue앱을 시작하기 위해 필수적이며, 앱의 진입점이 된다. 
```javascript
new Vue({
    el: '#app',
    data: {
        message: 'Hello, Vue!'
    }
})
```

* el : 인스턴스가 작동할 엘리먼트를 지정한다
* data : Vue 인스턴스 내부에서 사용할 데이터 변수를 정의
* Vue 인스턴스는 많은 옵셜을 가지며 그중에서 많이쓰이는게 데이터, 메서드, 라이플 사이클입니다.

 

## 데이터와 메서드

데이터는 다음과같이 해당 뷰인스턴스에 데이터를 바인딩 시킬 수 있다.

```javascript
new Vue({
    el: '#app',
    data:{
        message: 'Hello, Vue!',
        price : 10000
        //list, object
    }
})
```




메소드옵션은 다음과 같이 해당 뷰인스턴스에 사용될 메소드들을 정의하는공간이다. 다음과 같이 소스를 작성하게 되면 버튼 기능을 사용할 수 있게 된다.

```javascript
new Vue({
    el: '#app',
    methods:{
        showAlert : function(){
            alert('깜짝!');
        }
    }
})
```

```html
<button @click = "showAlert">클릭하세요.</button>
```

## 라이프 사이클
Vue 인스턴스는 대략적으로 **생성 -> 마운트 -> 업데이트 -> 파괴** 라이플 사이클을 가진다.

다음과 같이 mounted 옵션을 사용하게되면 뷰의 마운트 시점에 해당 옵션이 호출되게 된다.
```javascript

new Vue({
    el: '#app',
    mounted: function(){
        alert('Vue 인스턴스가 마운트 될 때 실행됩니다.');
        
    }
})

```

전체적인 라이플 사이클은 다음과 같습니다.

![](https://i.imgur.com/jqfs9k1.png)




## 계산된 속성


html에서 기능을 수행하기보다는 View에 집중해야한다.
```html
{{ message.split('').reverse().join('')}}
```

로직은 다음과같이 methods 옵션으로 관리하는게 가독성, 유지보수성이 좋다.
```html
{{ reverseMessage() }}
```

```javascript
new Vue({
    el: '#app',
    methods:{
        showAlert : function(){
            alert('깜짝!');
        }
    }
})
```

아니면 다음과 같이 computed 옵션을 사용해도 된다. methods와 거의 비슷해보이지만 함수형태가 아닌 데이터 형태이고 성능상에 이점이 있다. 또한 계산된 속성은 다음과 같이 getter 뿐만 아니라 setter를 가질 수 도 있습니다.

```html
<input v-model="fullName">
first name : {{ firstName }}
last name : {{ lastName }}
Full name : {{ fullName }}

```

```javascript
new Vue({
    el: '#app',
    data:{
        firstName: 'Lee',
        lastName: 'Sun-Hyoup'
    },
    computed:{
        fullNme : {
            get : function(){
                return this.firstName + ' ' + this.lastName;
            },
            set: function(newValue){
                var names = newValue.splite(' ');
                this.firstName = names[0];
                this.lastName = names[names.length - 1];
            }
        }
    }
})
```

## 감시된 속성
감시된 속성은 Vue 인스턴스의 데이터가 변경되는 시점을 감시해 메서드를 호출하는 기능이다. 
다음과 같이 input 이 있을때 이데이터가 변경됐을 때 뭔가 작업을 하고싶다면 아래와같이 watch 옵션을 이용할 수 있다.

```html
<input v-model="search">
```


```javascript
new Vue({
    el: '#app',
    data:{
        search: ''
    },
    watch:{
        search : function(value){
            //todo
        }
    }
})


## Model 사용법

Model은 데이터가 양방향 바인딩이 된다.

```
<input v-model="message" placeholder="여기를 수정해보세요">
<p>메시지: {{ message }}</p>
```

## bind 사용법

bind는 부모의 데이터를 자식 데이터에 바인딩할 떄 아래와같이 사용할 수 있다. bind는 단방향이다.

<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
v-bind에 대한 단축 구문을 사용하는 것이 더 간단합니다.

<child :my-message="parentMsg"></child>