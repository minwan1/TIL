## 컴포넌트

아래와같이 하면 simpleComponent로 데이터를 바인딩해서 전달할 수 있다.
```html
<simple-component message="Hello, Vue"></simple-component>
```

```javascript
var simpleComponent{
    props: ['message'],
    template: '<div>{{ message }}</div>'
}
```

### 네이밍 규칙
기본적으로 카멜케이스를 가져 가게된다. 예를들어 props 속성을 simpleMessage로 가져간다면 속성명을 아래와같이 simple-message로 해줘야한다.
```html
<simple-component simple-message="Hello, Vue"></simple-component>
```

```javascript
var simpleComponent{
    props: ['simpleMessage'],
    template: '<div>{{ simpleMessage }}</div>'
}
```

### 동적 props

아래와 같이 model을 선언하고 해당값을 바인딩하고자하는 컴퍼넌트에 바인딩하면 아들 컴퍼넌트와 데이터를 동적으로 공유할 수 있게 된다.

```html
    <input type="text" v-model="text">
    <br>
    정석버전 : <Component2 v-bind:msg="text"></Component2>
    간소화버전 : <Component2 :msg="text"></Component2>
```

```html

<template>
  <div>
    {{msg}}
  </div>
</template>

<script>
export default {
  name: 'Component2',
  props: ['msg']
}
</script>

```

### props 검증하기
다음과 같이 하면 넘어오는 props값을 validate할 수 있다.

```html
<Component4 :name="name" :price="price" message="test12345"></Component4>

<script>
export default {
  name: 'Component',
  components: {

    Component4
  },
  data () {
    return {
      name: 'test1234567',
      price: 5000
    }
  }
}
</script>
```

```html
<template>
  <div>
    <div>{{ name }}, {{ price }}원, {{ message }}</div>
  </div>
</template>

<script>
export default {
  name: 'Component4',
  props: {
    name: String,
    price: Number,
    message: [String, Number]
  }
}
</script>

```


### 사용자 정의 이벤트
부모 컴포넌트가 자식 컴포넌트에게 데이터를 전달할 떄에는 props를 사용한다. 반면 자식 컴포넌트는 부모 컴포넌트에게 데이터를 전달할 수는 없지만 이벤트를 통해 메시지를 전달할 수 있다. 이때 사용자 저으이 이벤트를 정의할 수 있다.

아래와같이 하면 Component5에 있는 increment이벤트가 발생하면 부모에 incrementTotal에 메소드가 호출되는 구조이다.
```html
    <Component5 v-on:increment="incrementTotal"></Component5>
    <Component5 v-on:increment="incrementTotal"></Component5>
    {{total}}

    <script>
import Component5 from '../core/Component5'

export default {
  name: 'Component',
  components: {
    Component5
  },
  data () {
    return {
      total: 0
    }
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
}
</script>
    
```


자식에 counter값은 별도에 생명주기를 가진다.

```html
<template>
  <div>
    <button @click="increment">{{ counter }}</button>
  </div>
</template>

<script>
export default {
  name: 'Component5',
  data: function () {
    return {counter: 0}
  },
  methods: {
    increment: function() {
      this.counter += 1;
      this.$emit('increment')
    }
  }
}
</script>
```